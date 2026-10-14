
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>카카오맵 장소 검색</title>

  <style>
    /* 전체 화면 기본 설정 */
    * {
      box-sizing: border-box;
    }

    html, body {
      margin: 0;
      width: 100%;
      height: 100%;
      font-family: Arial, sans-serif;
      background-color: #f5f7fa;
    }

    /* 화면을 좌우로 나누기 */
    .container {
      display: flex;
      width: 100%;
      height: 100vh;
      height: 100dvh;
    }

    /* 왼쪽 검색 영역 */
    .sidebar {
      width: 35%;
      min-width: 300px;
      padding: 24px;
      background-color: white;
      overflow-y: auto;
      box-shadow: 2px 0 8px rgba(0,0,0,0.08);
      z-index: 1;
    }

    h1 {
      font-size: 24px;
      color: #222;
      margin-top: 0;
    }

    .description {
      font-size: 14px;
      color: #777;
      margin-bottom: 20px;
    }

    /* 검색창 */
    .search-box {
      display: flex;
      gap: 8px;
      margin-bottom: 20px;
    }

    #searchInput {
      flex: 1;
      min-width: 0;
      padding: 12px;
      border: 1px solid #ddd;
      border-radius: 8px;
      font-size: 14px;
    }

    #searchButton {
      padding: 12px 18px;
      background-color: #fee500;
      border: none;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
    }

    #searchButton:hover {
      background-color: #f5dc00;
    }

    #searchButton:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }

    /* 현재 위치 버튼 */
    #locationButton {
      width: 100%;
      padding: 11px;
      margin-bottom: 15px;
      background-color: #f5f7fa;
      border: 1px solid #ddd;
      border-radius: 8px;
      cursor: pointer;
    }

    #locationButton:hover {
      background-color: #e9edf3;
    }

    /* 상태 메시지 */
    #statusMessage {
      font-size: 13px;
      color: #666;
      line-height: 1.6;
      margin-bottom: 15px;
    }

    /* 검색 결과 */
    .result-count {
      font-size: 13px;
      color: #777;
      margin-bottom: 10px;
    }

    .result-item {
      padding: 16px 10px;
      border-bottom: 1px solid #eee;
      cursor: pointer;
    }

    .result-item:hover {
      background-color: #fffbea;
    }

    .result-item h3 {
      margin: 0 0 8px;
      font-size: 16px;
    }

    .result-item p {
      margin: 4px 0;
      font-size: 13px;
      color: #666;
    }

    /* 오른쪽 지도 영역 */
    .map-container {
      flex: 1;
      min-width: 0;
      height: 100%;
      position: relative;
      background-color: #e9edf3;
    }

    #map {
      width: 100%;
      height: 100%;
    }

    /* 지도 로딩 안내 */
    #mapLoading {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 16px 22px;
      background-color: white;
      border-radius: 10px;
      box-shadow: 0 2px 12px rgba(0,0,0,0.1);
      font-size: 14px;
      text-align: center;
      z-index: 2;
    }

    /* 모바일 화면 */
    @media (max-width: 650px) {
      .container {
        flex-direction: column;
      }

      .sidebar {
        width: 100%;
        min-width: 0;
        height: 45%;
      }

      .map-container {
        height: 55%;
        flex: none;
      }
    }
  </style>
</head>

<body>

  <div class="container">

    <!-- 왼쪽 검색 영역 -->
    <aside class="sidebar">

      <h1>📍 장소 검색</h1>

      <p class="description">
        원하는 장소를 검색해 보세요!
      </p>

      <div class="search-box">
        <input
          type="text"
          id="searchInput"
          placeholder="예: 카페, 도서관, 학교"
        >

        <button id="searchButton" disabled>
          검색
        </button>
      </div>

      <button id="locationButton">
        📍 내 위치로 이동
      </button>

      <p id="statusMessage">
        카카오맵을 불러오는 중입니다...
      </p>

      <div id="resultList"></div>

    </aside>

    <!-- 오른쪽 지도 영역 -->
    <div class="map-container">

      <div id="map"></div>

      <div id="mapLoading">
        지도를 불러오는 중입니다...
      </div>

    </div>

  </div>

  <script>

    // 카카오 JavaScript API 키
    const kakaoApiKey = "78249eeff559719ed80f62909378e437";

    // 지도 관련 변수
    let map = null;
    let places = null;
    let infoWindow = null;
    let currentMarker = null;
    let markers = [];

    // 지도 준비 상태
    let mapReady = false;

    // 현재 위치 요청과 검색 결과가 충돌하지 않도록 관리
    let locationRequestId = 0;
    let searchRequestId = 0;

    // HTML 요소 가져오기
    const searchInput = document.getElementById("searchInput");
    const searchButton = document.getElementById("searchButton");
    const locationButton = document.getElementById("locationButton");
    const statusMessage = document.getElementById("statusMessage");
    const resultList = document.getElementById("resultList");
    const mapLoading = document.getElementById("mapLoading");

    // 기본 지도 위치: 서울시청
    const defaultLatitude = 37.5665;
    const defaultLongitude = 126.9780;

    // 상태 메시지 변경
    function updateStatus(message) {
      statusMessage.textContent = message;
    }

    // 지도 로딩 오류 처리
    function showMapError(message) {

      console.error(message);

      updateStatus(message);

      mapLoading.textContent = message;

      mapLoading.style.color = "#d93025";
    }

    // 카카오맵 SDK 불러오기
    function loadKakaoSdk() {

      updateStatus("카카오맵 SDK를 불러오는 중입니다...");

      const script = document.createElement("script");

      // 올바른 카카오맵 SDK 주소 사용
      script.src =
        "https://dapi.kakao.com/v2/maps/sdk.js" +
        "?appkey=" + encodeURIComponent(kakaoApiKey) +
        "&libraries=services" +
        "&autoload=false";

      script.async = true;

      // SDK 다운로드 완료
      script.onload = function() {

        // 카카오맵 객체가 정상적으로 생성되었는지 확인
        if (
          typeof kakao === "undefined" ||
          !kakao.maps ||
          typeof kakao.maps.load !== "function"
        ) {

          showMapError(
            "카카오맵 SDK 초기화에 실패했습니다. API 키와 웹 도메인 설정을 확인해주세요."
          );

          return;
        }

        // SDK 내부 초기화까지 완료한 후 지도 생성
        try {

          kakao.maps.load(function() {

            try {
              initializeMap();
            } catch (error) {

              showMapError(
                "지도 생성 오류: " + error.message
              );

            }

          });

        } catch (error) {

          showMapError(
            "카카오맵 초기화 오류: " + error.message
          );

        }
      };

      // SDK 다운로드 실패
      script.onerror = function() {

        showMapError(
          "카카오맵 SDK를 불러오지 못했습니다. 인터넷 연결, API 키, 카카오맵 사용 설정을 확인해주세요."
        );

      };

      document.head.appendChild(script);
    }

    // 지도 생성 함수
    function initializeMap() {

      updateStatus("지도를 생성하는 중입니다...");

      // 기본 위치 설정
      const defaultPosition = new kakao.maps.LatLng(
        defaultLatitude,
        defaultLongitude
      );

      // 지도 옵션
      const mapOptions = {
        center: defaultPosition,
        level: 5
      };

      // 지도 생성
      map = new kakao.maps.Map(
        document.getElementById("map"),
        mapOptions
      );

      // 장소 검색 객체 생성
      places = new kakao.maps.services.Places();

      // 장소 정보창 생성
      infoWindow = new kakao.maps.InfoWindow({
        zIndex: 1
      });

      // 지도 준비 완료
      mapReady = true;

      // 검색 버튼 활성화
      searchButton.disabled = false;

      // 로딩 화면 숨기기
      mapLoading.style.display = "none";

      updateStatus("지도가 준비되었습니다.");

      // 지도 크기 다시 계산
      map.relayout();

      // 현재 위치 요청
      getCurrentLocation();

    }

    // 현재 위치 가져오기
    function getCurrentLocation() {

      if (!mapReady) {
        updateStatus("지도가 아직 준비되지 않았습니다.");
        return;
      }

      // 위치 요청을 구분하기 위한 번호
      const requestId = ++locationRequestId;

      // 현재 지도 이동 상태 저장
      const currentSearchId = searchRequestId;

      // 브라우저 위치 기능 확인
      if (!navigator.geolocation) {

        updateStatus(
          "위치 기능을 지원하지 않아 서울시청을 기본 위치로 사용합니다."
        );

        return;
      }

      updateStatus("현재 위치를 확인하는 중입니다...");

      navigator.geolocation.getCurrentPosition(

        // 위치 확인 성공
        function(position) {

          // 오래된 위치 요청은 무시
          if (requestId !== locationRequestId) {
            return;
          }

          const latitude = position.coords.latitude;
          const longitude = position.coords.longitude;

          const currentPosition = new kakao.maps.LatLng(
            latitude,
            longitude
          );

          // 기존 현재 위치 마커 제거
          if (currentMarker) {
            currentMarker.setMap(null);
          }

          // 현재 위치 마커 생성
          currentMarker = new kakao.maps.Marker({
            map: map,
            position: currentPosition
          });

          // 검색 중이 아니라면 현재 위치로 지도 이동
          if (currentSearchId === searchRequestId) {

            map.setCenter(currentPosition);
            map.setLevel(5);

          }

          updateStatus(
            "현재 위치를 확인했습니다."
          );
        },

        // 위치 확인 실패
        function(error) {

          if (requestId !== locationRequestId) {
            return;
          }

          console.warn("위치 오류:", error.message);

          if (error.code === 1) {

            updateStatus(
              "위치 권한이 거부되었습니다. 현재 지도 위치를 유지합니다."
            );

          } else if (error.code === 2) {

            updateStatus(
              "현재 위치를 확인할 수 없습니다."
            );

          } else {

            updateStatus(
              "위치 확인 시간이 초과되었습니다."
            );

          }

        },

        // 위치 확인 옵션
        {
          enableHighAccuracy: true,
          timeout: 10000,
          maximumAge: 0
        }

      );
    }

    // 장소 검색 함수
    function searchPlaces() {

      if (!mapReady || !places) {

        alert("지도가 아직 준비되지 않았습니다.");

        return;
      }

      const keyword = searchInput.value.trim();

      // 검색어가 비어 있는 경우
      if (!keyword) {

        alert("검색어를 입력해주세요.");

        searchInput.focus();

        return;
      }

      const requestId = ++searchRequestId;

      updateStatus("장소를 검색하는 중입니다...");

      // 카카오 장소 검색 API 사용
      places.keywordSearch(
        keyword,
        function(data, status) {

          // 이전 검색 응답은 무시
          if (requestId !== searchRequestId) {
            return;
          }

          if (status === kakao.maps.services.Status.OK) {

            displayPlaces(data);

            updateStatus(
              "검색이 완료되었습니다."
            );

          } else if (
            status === kakao.maps.services.Status.ZERO_RESULT
          ) {

            removeMarkers();

            infoWindow.close();

            resultList.innerHTML = "";

            updateStatus(
              "검색 결과가 없습니다."
            );

          } else {

            updateStatus(
              "장소 검색에 실패했습니다. API 설정을 확인해주세요."
            );

            console.error("검색 오류:", status);

          }

        }
      );
    }

    // 검색 결과 표시
    function displayPlaces(data) {

      // 기존 검색 결과 초기화
      removeMarkers();

      infoWindow.close();

      resultList.innerHTML = "";

      // 검색된 장소들을 포함할 지도 범위
      const bounds = new kakao.maps.LatLngBounds();

      // 검색 결과 개수 표시
      const resultCount = document.createElement("p");

      resultCount.className = "result-count";

      resultCount.textContent =
        "검색 결과 " + data.length + "개 (현재 페이지)";

      resultList.appendChild(resultCount);

      // 장소별 검색 결과 생성
      data.forEach(function(place) {

        const position = new kakao.maps.LatLng(
          Number(place.y),
          Number(place.x)
        );

        // 지도에 마커 생성
        const marker = new kakao.maps.Marker({
          map: map,
          position: position
        });

        markers.push(marker);

        bounds.extend(position);

        // 검색 결과 항목 생성
        const item = document.createElement("div");

        item.className = "result-item";

        const title = document.createElement("h3");

        title.textContent = place.place_name;

        const address = document.createElement("p");

        address.textContent =
          place.road_address_name ||
          place.address_name ||
          "주소 정보 없음";

        const phone = document.createElement("p");

        phone.textContent =
          place.phone || "전화번호 정보 없음";

        // 검색 결과 목록에 추가
        item.appendChild(title);
        item.appendChild(address);
        item.appendChild(phone);

        resultList.appendChild(item);

        // 장소를 선택했을 때 실행
        function selectPlace() {

          // 지도 중심 이동
          map.setCenter(position);

          // 지도 확대 레벨 설정
          map.setLevel(5);

          // 장소 이름을 안전하게 표시
          const content = document.createElement("div");

          content.style.padding = "10px";
          content.style.fontSize = "13px";
          content.style.whiteSpace = "nowrap";

          content.textContent = place.place_name;

          // 정보창 표시
          infoWindow.setContent(content);

          infoWindow.open(map, marker);

        }

        // 검색 결과 클릭 이벤트
        item.addEventListener("click", selectPlace);

        // 지도 마커 클릭 이벤트
        kakao.maps.event.addListener(
          marker,
          "click",
          selectPlace
        );

      });

      // 검색 결과가 지도에 모두 보이도록 설정
      if (data.length > 0) {

        map.setBounds(bounds);

      }

    }

    // 이전 검색 마커 삭제
    function removeMarkers() {

      markers.forEach(function(marker) {

        marker.setMap(null);

      });

      markers = [];

    }

    // 검색 버튼 클릭
    searchButton.addEventListener(
      "click",
      searchPlaces
    );

    // 엔터키로 검색
    searchInput.addEventListener(
      "keydown",
      function(event) {

        if (event.key === "Enter") {

          searchPlaces();

        }

      }
    );

    // 현재 위치 버튼 클릭
    locationButton.addEventListener(
      "click",
      getCurrentLocation
    );

    // 브라우저 크기가 바뀌면 지도 크기 재설정
    window.addEventListener("resize", function() {

      if (mapReady) {

        map.relayout();

      }

    });

    // HTML이 준비된 후 카카오맵 SDK 불러오기
    if (document.readyState === "loading") {

      document.addEventListener(
        "DOMContentLoaded",
        loadKakaoSdk
      );

    } else {

      loadKakaoSdk();

    }

  </script>

</body>
</html>
