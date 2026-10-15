
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
      background: #f5f7fa;
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
      background: white;
      overflow-y: auto;
      box-shadow: 2px 0 8px rgba(0,0,0,.08);
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
      background: #fee500;
      border: 0;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
    }

    button:disabled {
      opacity: .5;
      cursor: not-allowed;
    }

    /* 현재 위치 버튼 */
    #locationButton {
      width: 100%;
      padding: 11px;
      margin-bottom: 10px;
      background: #f5f7fa;
      border: 1px solid #ddd;
      border-radius: 8px;
      cursor: pointer;
    }

    /* 새로 추가한 영화관 검색 버튼 */
    #cinemaButton {
      width: 100%;
      padding: 12px;
      margin-bottom: 15px;
      background: #e8efff;
      border: 1px solid #b8c9ff;
      border-radius: 8px;
      color: #234d9c;
      font-weight: bold;
      cursor: pointer;
    }

    #cinemaButton:hover {
      background: #d8e5ff;
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
      background: #fffbea;
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
      background: #e9edf3;
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
      background: white;
      border-radius: 10px;
      box-shadow: 0 2px 12px rgba(0,0,0,.1);
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
          id="searchInput"
          type="text"
          placeholder="예: 카페, 도서관, 학교"
        >
        <button id="searchButton" disabled>검색</button>
      </div>

      <!-- 현재 위치로 이동 -->
      <button id="locationButton" disabled>
        📍 내 위치로 이동
      </button>

      <!-- 새로 추가한 영화관 검색 버튼 -->
      <button id="cinemaButton" disabled>
        🎬 내 주변 영화관 찾기 (5km)
      </button>

      <p id="statusMessage">
        카카오맵을 불러오는 중입니다...
      </p>

      <!-- 검색 결과 목록 -->
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
    let currentInfoWindow = null;
    let markers = [];
    let mapReady = false;

    // 현재 위치 저장 변수
    let currentPosition = null;

    // 검색 요청 구분 번호
    let searchRequestId = 0;
    let locationRequestId = 0;

    // 영화관 검색 반경: 5km
    const cinemaRadius = 5000;

    // HTML 요소 가져오기
    const searchInput = document.getElementById("searchInput");
    const searchButton = document.getElementById("searchButton");
    const locationButton = document.getElementById("locationButton");
    const cinemaButton = document.getElementById("cinemaButton");
    const statusMessage = document.getElementById("statusMessage");
    const resultList = document.getElementById("resultList");
    const mapLoading = document.getElementById("mapLoading");

    // 상태 메시지 변경
    function updateStatus(message) {
      statusMessage.textContent = message;
    }

    // 지도 오류 표시
    function showMapError(message) {
      console.error(message);
      updateStatus(message);
      mapLoading.textContent = message;
      mapLoading.style.color = "red";
    }

    // 카카오맵 SDK 로딩
    function loadKakaoSdk() {

      const script = document.createElement("script");

      script.src =
        "https://dapi.kakao.com/v2/maps/sdk.js?appkey=" +
        encodeURIComponent(kakaoApiKey) +
        "&libraries=services&autoload=false";

      script.async = true;

      // SDK 로딩 완료 후 지도 생성
      script.onload = function() {

        if (!window.kakao || !kakao.maps) {
          showMapError("카카오맵 초기화에 실패했습니다.");
          return;
        }

        kakao.maps.load(function() {
          try {
            initializeMap();
          } catch (error) {
            showMapError("지도 생성 오류: " + error.message);
          }
        });
      };

      script.onerror = function() {
        showMapError("SDK 로딩 실패. API 설정을 확인하세요.");
      };

      document.head.appendChild(script);
    }

    // 지도 생성
    function initializeMap() {

      // 기본 위치: 서울 시청
      const defaultPosition = new kakao.maps.LatLng(
        37.5665,
        126.9780
      );

      // 지도 생성 및 확대 레벨 5
      map = new kakao.maps.Map(
        document.getElementById("map"),
        {
          center: defaultPosition,
          level: 5
        }
      );

      // 장소 검색 객체 생성
      places = new kakao.maps.services.Places();

      // 장소 정보창 생성
      infoWindow = new kakao.maps.InfoWindow({
        zIndex: 1
      });

      // 현재 위치 정보창 생성
      currentInfoWindow = new kakao.maps.InfoWindow({
        removable: true,
        zIndex: 2
      });

      // 지도 준비 완료
      mapReady = true;

      searchButton.disabled = false;
      locationButton.disabled = false;
      cinemaButton.disabled = false;

      mapLoading.style.display = "none";

      map.relayout();

      // 페이지가 열리면 현재 위치 확인
      getCurrentLocation();
    }

    // 현재 위치 마커 표시
    function displayCurrentMarker(position, moveMap = true) {

      // 기존 마커 제거
      if (currentMarker) {
        currentMarker.setMap(null);
      }

      currentInfoWindow.close();

      // 현재 위치 마커 생성
      currentMarker = new kakao.maps.Marker({
        map: map,
        position: position,
        title: "현재 위치"
      });

      // 현재 위치 말풍선 표시
      currentInfoWindow.setContent(
        '<div style="padding:8px;white-space:nowrap;">' +
        '여기에 계신가요?!' +
        '</div>'
      );

      currentInfoWindow.open(map, currentMarker);

      // 필요한 경우 현재 위치로 이동
      if (moveMap) {
        map.setCenter(position);
        map.setLevel(5);
      }
    }

    // 현재 위치 가져오기
    function getCurrentLocation() {

      if (!mapReady) return;

      const requestId = ++locationRequestId;
      const searchIdAtStart = searchRequestId;

      // 위치 기능 사용 가능 여부 확인
      if (!navigator.geolocation || !window.isSecureContext) {
        updateStatus(
          "현재 위치 기능은 HTTPS 또는 localhost에서 사용할 수 있습니다."
        );
        return;
      }

      updateStatus("현재 위치를 확인하는 중입니다...");

      navigator.geolocation.getCurrentPosition(

        function(position) {

          if (requestId !== locationRequestId) return;

          // 현재 위치 좌표 저장
          const latitude = position.coords.latitude;
          const longitude = position.coords.longitude;

          currentPosition = new kakao.maps.LatLng(
            latitude,
            longitude
          );

          // 검색 결과를 보고 있으면 지도를 이동하지 않음
          const moveMap = searchIdAtStart === searchRequestId;

          // 현재 위치 마커 표시
          displayCurrentMarker(currentPosition, moveMap);

          updateStatus("현재 위치를 확인했습니다.");
        },

        function(error) {

          if (requestId !== locationRequestId) return;

          console.warn("위치 오류:", error.message);

          if (error.code === 1) {
            updateStatus("위치 권한이 거부되었습니다.");
          } else {
            updateStatus("현재 위치를 확인할 수 없습니다.");
          }
        },

        {
          enableHighAccuracy: true,
          timeout: 10000,
          maximumAge: 0
        }
      );
    }

    // 기존 검색 마커 제거
    function removeMarkers() {

      markers.forEach(function(marker) {
        marker.setMap(null);
      });

      markers = [];
    }

    // 일반 장소 검색
    function searchPlaces() {

      if (!mapReady) return;

      const keyword = searchInput.value.trim();

      if (!keyword) {
        alert("검색어를 입력해주세요.");
        return;
      }

      const requestId = ++searchRequestId;

      updateStatus("장소를 검색하는 중입니다...");

      places.keywordSearch(keyword, function(data, status) {

        if (requestId !== searchRequestId) return;

        if (status === kakao.maps.services.Status.OK) {

          displayPlaces(data);
          updateStatus("검색이 완료되었습니다.");

        } else if (
          status === kakao.maps.services.Status.ZERO_RESULT
        ) {

          removeMarkers();
          infoWindow.close();
          resultList.innerHTML = "";
          updateStatus("검색 결과가 없습니다.");

        } else {

          updateStatus("장소 검색에 실패했습니다.");

        }
      });
    }

    // 새로 추가한 주변 영화관 검색 함수
    function searchNearbyCinemas() {

      if (!mapReady) return;

      // 현재 위치가 없으면 영화관 검색 불가
      if (!currentPosition) {
        alert("먼저 현재 위치 권한을 허용해주세요.");
        getCurrentLocation();
        return;
      }

      const requestId = ++searchRequestId;

      updateStatus("주변 영화관을 검색하는 중입니다...");

      // 영화관 검색 결과를 저장할 배열
      const cinemaResults = [];

      // 검색 페이지 수
      let checkedPages = 0;
      const maxPages = 3;

      // 현재 위치를 기준으로 영화관 검색
      places.keywordSearch(
        "영화관",

        function(data, status, pagination) {

          if (requestId !== searchRequestId) return;

          if (status === kakao.maps.services.Status.OK) {

            // 검색 결과 중 영화관에 해당하는 장소 선택
            const cinemas = data.filter(function(place) {

              const category = place.category_name || "";

              return (
                category.includes("영화관") ||
                category.includes("극장")
              );
            });

            // 영화관 검색 결과 저장
            cinemaResults.push(...cinemas);

            checkedPages++;

            // 다음 페이지가 있다면 추가 검색
            if (
              pagination.hasNextPage &&
              checkedPages < maxPages
            ) {
              pagination.nextPage();
              return;
            }

            // 검색 완료 후 결과 표시
            finishCinemaSearch();

          } else if (
            status === kakao.maps.services.Status.ZERO_RESULT
          ) {

            finishCinemaSearch();

          } else {

            updateStatus("영화관 검색 중 오류가 발생했습니다.");
          }
        },

        {
          // 현재 위치를 검색 중심으로 지정
          location: currentPosition,

          // 반경 5km
          radius: cinemaRadius,

          // 현재 위치에서 가까운 순서로 정렬
          sort: kakao.maps.services.SortBy.DISTANCE
        }
      );

      // 영화관 검색 완료 처리
      function finishCinemaSearch() {

        if (requestId !== searchRequestId) return;

        // 같은 영화관이 중복되면 제거
        const uniqueCinemas = Array.from(
          new Map(
            cinemaResults.map(function(place) {
              return [place.id, place];
            })
          ).values()
        );

        // 결과가 없을 때
        if (uniqueCinemas.length === 0) {

          removeMarkers();
          infoWindow.close();
          resultList.innerHTML = "";

          updateStatus(
            "반경 5km 내에서 검색된 영화관이 없습니다."
          );

          return;
        }

        // 검색 결과 표시
        displayPlaces(uniqueCinemas);

        updateStatus(
          "반경 5km 내 영화관 " +
          uniqueCinemas.length +
          "개를 찾았습니다."
        );
      }
    }

    // 검색 결과 표시 함수
    function displayPlaces(data) {

      // 기존 검색 결과 제거
      removeMarkers();
      infoWindow.close();
      currentInfoWindow.close();
      resultList.innerHTML = "";

      // 검색 결과 지도 범위
      const bounds = new kakao.maps.LatLngBounds();

      // 검색 결과 개수
      const count = document.createElement("p");
      count.className = "result-count";

      count.textContent =
        "검색 결과 " + data.length + "개";

      resultList.appendChild(count);

      // 검색 결과를 하나씩 표시
      data.forEach(function(place) {

        // 장소 위치
        const position = new kakao.maps.LatLng(
          Number(place.y),
          Number(place.x)
        );

        // 지도 마커 생성
        const marker = new kakao.maps.Marker({
          map: map,
          position: position,
          title: place.place_name
        });

        markers.push(marker);
        bounds.extend(position);

        // 검색 결과 목록 생성
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

        item.append(title, address, phone);

        // 장소 선택 함수
        function selectPlace() {

          map.setCenter(position);
          map.setLevel(5);

          // 장소 이름 말풍선
          const content = document.createElement("div");
          content.style.padding = "10px";
          content.style.whiteSpace = "nowrap";
          content.textContent = place.place_name;

          infoWindow.setContent(content);
          infoWindow.open(map, marker);
        }

        // 목록 클릭
        item.addEventListener("click", selectPlace);

        // 마커 클릭
        kakao.maps.event.addListener(
          marker,
          "click",
          selectPlace
        );

        resultList.appendChild(item);
      });

      // 모든 검색 결과가 보이도록 지도 조정
      if (data.length > 0) {
        map.setBounds(bounds);
      }
    }

    // 검색 버튼 이벤트
    searchButton.addEventListener("click", searchPlaces);

    // 엔터키 검색
    searchInput.addEventListener("keydown", function(event) {
      if (event.key === "Enter") {
        searchPlaces();
      }
    });

    // 내 위치로 이동 버튼
    locationButton.addEventListener(
      "click",
      getCurrentLocation
    );

    // 주변 영화관 찾기 버튼
    cinemaButton.addEventListener(
      "click",
      searchNearbyCinemas
    );

    // 브라우저 크기 변경 시 지도 재설정
    window.addEventListener("resize", function() {
      if (mapReady) {
        map.relayout();
      }
    });

    // HTML 로딩 후 카카오맵 SDK 실행
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
