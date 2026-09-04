<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>학생 계정 & 임시 비밀번호 조회</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- 교실 스크린 투사용 QR 코드 라이브러리 -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Pretendard:wght@400;500;600;700&display=swap">
  <style>
    body {
      font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, system-ui, Roboto, sans-serif;
    }
    .custom-shadow {
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05), 0 8px 10px -6px rgba(0, 0, 0, 0.05);
    }
    .animate-pop {
      animation: popIn 0.2s cubic-bezier(0.16, 1, 0.3, 1) forwards;
    }
    @keyframes popIn {
      from { transform: scale(0.96); opacity: 0; }
      to { transform: scale(1); opacity: 1; }
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col justify-between antialiased">
  <!-- 상단 헤더 -->
  <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm">
    <div class="max-w-3xl mx-auto px-4 py-3 flex items-center justify-between">
      <div class="flex items-center space-x-2.5">
        <div class="w-9 h-9 rounded-xl bg-indigo-600 flex items-center justify-center text-white font-bold shadow-md shadow-indigo-200">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path>
          </svg>
        </div>
        <div>
          <h1 class="text-base font-bold text-slate-900 leading-tight">학생 계정 정보 조회</h1>
          <p class="text-xs text-slate-500">충남교육청 AI 계정 발급 조회 시스템</p>
        </div>
      </div>

      <!-- 상단 액션 버튼 (공유 & 명단) -->
      <div class="flex items-center gap-2">
        <button 
          id="sharePageBtn" 
          type="button" 
          class="text-xs font-semibold px-3 py-1.5 rounded-lg bg-indigo-50 border border-indigo-200 text-indigo-700 hover:bg-indigo-100 transition flex items-center gap-1.5 shadow-sm"
          title="학생들에게 페이지 링크 및 QR코드 공유하기"
        >
          <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.368 2.684 3 3 0 00-5.368-2.684z"></path>
          </svg>
          <span>학생 공유하기</span>
        </button>

        <button 
          id="toggleListBtn" 
          type="button" 
          class="text-xs font-medium px-2.5 py-1.5 rounded-lg border border-slate-200 text-slate-600 hover:bg-slate-100 transition hidden sm:inline-block"
        >
          전체 명단
        </button>
      </div>
    </div>
  </header>

  <!-- 메인 컨텐츠 영역 -->
  <main class="max-w-xl w-full mx-auto p-4 sm:p-6 flex-1 flex flex-col justify-center">
    
    <!-- 빠른 링크 공유 배너 -->
    <div class="mb-4 bg-gradient-to-r from-indigo-500/10 via-purple-500/10 to-pink-500/10 border border-indigo-100 rounded-xl p-3 flex items-center justify-between">
      <div class="flex items-center space-x-2 text-xs text-indigo-900">
        <span class="flex h-2 w-2 relative">
          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-indigo-400 opacity-75"></span>
          <span class="relative inline-flex rounded-full h-2 w-2 bg-indigo-600"></span>
        </span>
        <span class="font-medium">학급 단톡방이나 칠판에 링크를 공유해보세요!</span>
      </div>
      <button 
        type="button" 
        onclick="copyShareUrl()" 
        class="text-xs font-semibold px-2.5 py-1 bg-white hover:bg-indigo-50 text-indigo-700 rounded-md border border-indigo-200 transition shadow-xs flex items-center gap-1 flex-shrink-0"
      >
        <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.828 10.172a4 4 0 00-5.656 0l-4 4a4 4 0 105.656 5.656l1.102-1.101m-.758-4.899a4 4 0 005.656 0l4-4a4 4 0 00-5.656-5.656l-1.1 1.1"></path>
        </svg>
        링크 복사
      </button>
    </div>

    <!-- 조회 카드 -->
    <div class="bg-white rounded-2xl p-6 sm:p-8 custom-shadow border border-slate-200/80 mb-6">
      <div class="mb-6 text-center">
        <span class="inline-block px-3 py-1 bg-indigo-50 text-indigo-700 text-xs font-semibold rounded-full mb-2">
          2학년 전용 조회
        </span>
        <h2 class="text-xl sm:text-2xl font-bold text-slate-900">내 계정 정보 확인하기</h2>
        <p class="text-sm text-slate-500 mt-1">반과 번호를 선택한 후 조회하면 아이디와 임시 비밀번호가 나타납니다.</p>
      </div>

      <form id="searchForm" class="space-y-4" onsubmit="return false;">
        <div class="grid grid-cols-2 gap-3">
          <!-- 반 선택 -->
          <div>
            <label for="classSelect" class="block text-xs font-semibold text-slate-700 mb-1.5">반 (학급)</label>
            <div class="relative">
              <select id="classSelect" class="w-full px-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-slate-900 font-medium text-sm focus:bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition">
                <option value="">반 선택</option>
                <option value="5">5반</option>
                <option value="6">6반</option>
                <option value="7">7반</option>
                <option value="8">8반</option>
                <option value="9">9반</option>
              </select>
            </div>
          </div>

          <!-- 번호 입력 -->
          <div>
            <label for="numberInput" class="block text-xs font-semibold text-slate-700 mb-1.5">번호</label>
            <input 
              type="number" 
              id="numberInput" 
              placeholder="예: 7" 
              min="1" 
              max="50"
              class="w-full px-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-slate-900 font-medium text-sm focus:bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition"
            >
          </div>
        </div>

        <!-- 이름 확인 (선택 사항) -->
        <div>
          <div class="flex justify-between items-center mb-1.5">
            <label for="nameInput" class="block text-xs font-semibold text-slate-700">이름 (선택 입력)</label>
            <span class="text-[11px] text-slate-400">본인 확인용</span>
          </div>
          <input 
            type="text" 
            id="nameInput" 
            placeholder="이름을 입력하면 본인 일치 여부를 검증합니다" 
            class="w-full px-3.5 py-2.5 bg-slate-50 border border-slate-300 rounded-xl text-slate-900 font-medium text-sm focus:bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent transition"
          >
        </div>

        <button 
          id="searchBtn" 
          type="button" 
          class="w-full py-3 px-4 bg-indigo-600 hover:bg-indigo-700 active:scale-[0.99] text-white font-semibold rounded-xl text-sm shadow-md shadow-indigo-200 transition duration-150 flex items-center justify-center space-x-2 mt-2"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path>
          </svg>
          <span>계정 정보 조회하기</span>
        </button>
      </form>

      <!-- 조회 결과 영역 -->
      <div id="resultCard" class="hidden mt-6 pt-6 border-t border-slate-200 animate-pop">
        <div class="bg-indigo-50/60 border border-indigo-100 rounded-xl p-4 sm:p-5">
          <div class="flex items-center justify-between pb-3 border-b border-indigo-100/80 mb-3.5">
            <div>
              <span class="text-xs font-semibold text-indigo-700" id="resClassNum">2학년 5반 2번</span>
              <h3 class="text-lg font-bold text-slate-900" id="resName">권구훈 학생</h3>
            </div>
            <span class="px-2.5 py-1 bg-emerald-100 text-emerald-800 text-xs font-semibold rounded-full flex items-center gap-1">
              <span class="w-1.5 h-1.5 rounded-full bg-emerald-500"></span>
              조회 완료
            </span>
          </div>

          <div class="space-y-3">
            <!-- 아이디 박스 -->
            <div>
              <div class="flex justify-between items-center mb-1">
                <label class="block text-xs font-semibold text-slate-500">아이디 (이메일)</label>
                <span class="text-[11px] text-indigo-600 font-medium">복사 시 '@' 앞 아이디만 복사됩니다</span>
              </div>
              <div class="flex items-center justify-between bg-white px-3.5 py-2.5 rounded-lg border border-slate-200">
                <div class="truncate mr-2">
                  <span id="resUsername" class="text-sm font-mono font-bold text-indigo-700 bg-indigo-50/80 px-1.5 py-0.5 rounded">guhun5</span>
                  <span id="resDomain" class="text-sm font-mono text-slate-400">@.ai.cne.go.kr</span>
                </div>
                
                <div class="flex items-center space-x-1.5 flex-shrink-0">
                  <!-- 핵심 요구사항: @ 이전 아이디만 복사되는 버튼 -->
                  <button 
                    type="button" 
                    onclick="copyOnlyUsername()" 
                    class="text-xs px-2.5 py-1 bg-indigo-600 hover:bg-indigo-700 text-white rounded-md font-semibold transition flex items-center gap-1 shadow-xs"
                    title="@ 앞의 아이디만 복사"
                  >
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
                    </svg>
                    아이디 복사
                  </button>

                  <!-- 보조 버튼: 전체 이메일 주소 복사 -->
                  <button 
                    type="button" 
                    onclick="copyFullEmail()" 
                    class="text-xs px-2 py-1 bg-slate-100 hover:bg-slate-200 text-slate-600 rounded-md font-medium transition"
                    title="이메일 전체 주소(@포함) 복사"
                  >
                    전체주소
                  </button>
                </div>
              </div>
            </div>

            <!-- 임시 비밀번호 박스 -->
            <div>
              <label class="block text-xs font-semibold text-slate-500 mb-1">임시 비밀번호</label>
              <div class="flex items-center justify-between bg-white px-3.5 py-2.5 rounded-lg border border-slate-200">
                <div class="flex items-center space-x-2 truncate mr-2">
                  <span id="resPw" class="text-sm font-mono font-bold text-slate-800 select-all">!1q2w3e4r</span>
                </div>
                <div class="flex items-center space-x-1.5 flex-shrink-0">
                  <button type="button" id="togglePwMaskBtn" onclick="togglePasswordVisibility()" class="text-xs px-2 py-1 text-slate-500 hover:text-slate-700 rounded transition" title="비밀번호 숨김/표시">
                    <svg id="eyeIcon" class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>
                    </svg>
                  </button>
                  <button type="button" onclick="copyPassword()" class="text-xs px-2.5 py-1 bg-slate-100 hover:bg-indigo-50 hover:text-indigo-600 rounded-md font-semibold text-slate-600 transition flex items-center gap-1">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
                    </svg>
                    복사
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- 유의 사항 안내 -->
          <div class="mt-4 p-3 bg-white/80 rounded-lg text-xs text-slate-600 space-y-1">
            <p class="flex items-start gap-1.5 font-medium text-amber-700">
              <span class="inline-block mt-0.5 font-bold">⚠️</span>
              첫 로그인 후 반드시 본인만의 비밀번호로 재설정해 주세요.
            </p>
            <p class="text-slate-500 pl-4">도메인 끝 주소는 <strong>@.ai.cne.go.kr</strong> 형식입니다.</p>
          </div>
        </div>
      </div>

      <!-- 에러 박스 -->
      <div id="errorBox" class="hidden mt-4 p-3.5 rounded-xl bg-rose-50 border border-rose-200 text-rose-700 text-xs font-medium flex items-center gap-2">
        <svg class="w-4 h-4 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
        </svg>
        <span id="errorMsg">일치하는 학생 정보를 찾을 수 없습니다. 반과 번호를 다시 확인해 주세요.</span>
      </div>
    </div>

    <!-- 공유 안내 모달 (QR 코드 & URL 복사) -->
    <div id="shareModal" class="hidden fixed inset-0 z-50 bg-slate-900/50 backdrop-blur-sm flex items-center justify-center p-4">
      <div class="bg-white rounded-2xl max-w-sm w-full p-6 shadow-2xl border border-slate-200 animate-pop text-center relative">
        <button type="button" id="closeShareBtn" class="absolute top-4 right-4 p-1.5 text-slate-400 hover:text-slate-600 rounded-lg hover:bg-slate-100 transition">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>

        <div class="w-12 h-12 rounded-full bg-indigo-50 text-indigo-600 mx-auto flex items-center justify-center mb-3">
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v1m6 11h2m-6 0h-2v4m0-11v3m0 0h.01M12 12h4.01M16 20h4M4 12h4m12 0h.01M5 8h2a1 1 0 001-1V5a1 1 0 00-1-1H5a1 1 0 00-1 1v2a1 1 0 001 1zm12 0h2a1 1 0 001-1V5a1 1 0 00-1-1h-2a1 1 0 00-1 1v2a1 1 0 001 1zM5 20h2a1 1 0 001-1v-2a1 1 0 00-1-1H5a1 1 0 00-1 1v2a1 1 0 001 1z"></path>
          </svg>
        </div>

        <h3 class="text-lg font-bold text-slate-900">학생 공유용 QR & 링크</h3>
        <p class="text-xs text-slate-500 mt-1 mb-4">학생들이 스마트폰 카메라로 QR을 스캔하거나 링크를 통해 바로 접속할 수 있습니다.</p>

        <!-- QR 코드 렌더링 컨테이너 -->
        <div class="p-4 bg-slate-50 border border-slate-200 rounded-xl inline-block mx-auto mb-4">
          <div id="qrcodeContainer" class="flex items-center justify-center"></div>
        </div>

        <div class="space-y-2">
          <button 
            type="button" 
            onclick="copyShareUrl()" 
            class="w-full py-2.5 px-4 bg-indigo-600 hover:bg-indigo-700 text-white font-semibold rounded-xl text-xs shadow-md shadow-indigo-200 transition flex items-center justify-center gap-2"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"></path>
            </svg>
            <span>페이지 링크 복사하기</span>
          </button>
          
          <p class="text-[11px] text-slate-400">교실 TV 화면이나 칠판에 이 창을 띄워두셔도 좋습니다.</p>
        </div>
      </div>
    </div>

    <!-- 전체 명단 모달 -->
    <div id="fullListModal" class="hidden fixed inset-0 z-50 bg-slate-900/40 backdrop-blur-sm flex items-center justify-center p-4">
      <div class="bg-white rounded-2xl max-w-2xl w-full max-h-[85vh] flex flex-col shadow-2xl border border-slate-200 overflow-hidden">
        <div class="px-5 py-4 border-b border-slate-200 flex items-center justify-between">
          <div>
            <h3 class="font-bold text-slate-900 text-base">전체 학생 계정 목록 (2학년)</h3>
            <p class="text-xs text-slate-500">총 32명 등록 완료</p>
          </div>
          <button type="button" id="closeListBtn" class="p-1.5 text-slate-400 hover:text-slate-600 rounded-lg hover:bg-slate-100 transition">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
            </svg>
          </button>
        </div>

        <div class="p-4 border-b border-slate-100 bg-slate-50">
          <input 
            type="text" 
            id="tableFilterInput" 
            placeholder="이름, 반, 번호 검색 (예: 5반, 강대현)..." 
            class="w-full px-3.5 py-2 text-xs bg-white border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500"
          >
        </div>

        <div class="overflow-y-auto flex-1 p-4">
          <table class="w-full text-left text-xs">
            <thead class="bg-slate-100 text-slate-600 uppercase font-semibold sticky top-0">
              <tr>
                <th class="p-2.5 rounded-l-lg">학급</th>
                <th class="p-2.5">성명</th>
                <th class="p-2.5">아이디</th>
                <th class="p-2.5 rounded-r-lg">임시 비밀번호</th>
              </tr>
            </thead>
            <tbody id="studentTableBody" class="divide-y divide-slate-100">
              <!-- Dynamically populated -->
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </main>

  <!-- 하단 토스트 알림창 -->
  <div id="toast" class="fixed bottom-5 left-1/2 -translate-x-1/2 z-50 bg-slate-900 text-white text-xs font-semibold px-4 py-2.5 rounded-xl shadow-lg opacity-0 pointer-events-none transition-all duration-200 flex items-center gap-2">
    <svg class="w-4 h-4 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
    </svg>
    <span id="toastMsg">복사되었습니다.</span>
  </div>

  <footer class="text-center py-4 text-xs text-slate-400 border-t border-slate-200 bg-white">
    충남교육청 학생 계정 배부 지원 시스템 &copy; 2학년
  </footer>

  <script>
    // 이미지 명단 기반 32명 학생 데이터베이스
    const studentData = [
      { grade: 2, classNum: 5, num: 2, name: "권구훈", email: "guhun5@.ai.cne.go.kr", pw: "!1q2w3e4r" },
      { grade: 2, classNum: 5, num: 3, name: "권민기", email: "mingi5@.ai.cne.go.kr", pw: "!1q2w3e5r" },
      { grade: 2, classNum: 5, num: 5, name: "김윤성", email: "yunseong5@.ai.cne.go.kr", pw: "!1q2w3e6r" },
      { grade: 2, classNum: 5, num: 16, name: "우예주", email: "yeju5@.ai.cne.go.kr", pw: "!1q2w3e7r" },
      { grade: 2, classNum: 5, num: 18, name: "윤지원", email: "jiwon5@.ai.cne.go.kr", pw: "!1q2w3e8r" },
      { grade: 2, classNum: 5, num: 32, name: "함강윤", email: "gangyun5@.ai.cne.go.kr", pw: "!1q2w3e9r" },

      { grade: 2, classNum: 6, num: 4, name: "김승우", email: "seungu6@.ai.cne.go.kr", pw: "!1q2w3e10r" },
      { grade: 2, classNum: 6, num: 7, name: "김준규", email: "jungyu6@.ai.cne.go.kr", pw: "!1q2w3e11r" },
      { grade: 2, classNum: 6, num: 15, name: "배정우", email: "jeongu6@.ai.cne.go.kr", pw: "!1q2w3e12r" },
      { grade: 2, classNum: 6, num: 17, name: "유효선", email: "hyoseon6@.ai.cne.go.kr", pw: "!1q2w3e13r" },
      { grade: 2, classNum: 6, num: 22, name: "이준혁", email: "junhyeok6@.ai.cne.go.kr", pw: "!1q2w3e14r" },
      { grade: 2, classNum: 6, num: 23, name: "이채윤", email: "chaeyun6@.ai.cne.go.kr", pw: "!1q2w3e15r" },
      { grade: 2, classNum: 6, num: 32, name: "홍효주", email: "hyoju6@.ai.cne.go.kr", pw: "!1q2w3e16r" },

      { grade: 2, classNum: 7, num: 4, name: "김리아", email: "ria7@.ai.cne.go.kr", pw: "!1q2w3e17r" },
      { grade: 2, classNum: 7, num: 13, name: "성시훈", email: "sihun7@.ai.cne.go.kr", pw: "!1q2w3e18r" },
      { grade: 2, classNum: 7, num: 14, name: "신무빈", email: "mubin7@.ai.cne.go.kr", pw: "!1q2w3e19r" },
      { grade: 2, classNum: 7, num: 15, name: "신서균", email: "seogyun7@.ai.cne.go.kr", pw: "!1q2w3e20r" },
      { grade: 2, classNum: 7, num: 17, name: "안성현", email: "seonghyeon7@.ai.cne.go.kr", pw: "!1q2w3e21r" },
      { grade: 2, classNum: 7, num: 24, name: "이재우", email: "jaeu7@.ai.cne.go.kr", pw: "!1q2w3e22r" },
      { grade: 2, classNum: 7, num: 29, name: "최지율", email: "jiyul7@.ai.cne.go.kr", pw: "!1q2w3e23r" },

      { grade: 2, classNum: 8, num: 14, name: "서유리", email: "yuri8@.ai.cne.go.kr", pw: "!1q2w3e24r" },
      { grade: 2, classNum: 8, num: 18, name: "이민하", email: "minha8@.ai.cne.go.kr", pw: "!1q2w3e25r" },
      { grade: 2, classNum: 8, num: 20, name: "이소율", email: "soyul8@.ai.cne.go.kr", pw: "!1q2w3e26r" },
      { grade: 2, classNum: 8, num: 26, name: "정도원", email: "dowon8@.ai.cne.go.kr", pw: "!1q2w3e27r" },
      { grade: 2, classNum: 8, num: 28, name: "진홍인", email: "hongin8@.ai.cne.go.kr", pw: "!1q2w3e28r" },
      { grade: 2, classNum: 8, num: 31, name: "최혜원", email: "hyewon8@.ai.cne.go.kr", pw: "!1q2w3e29r" },

      { grade: 2, classNum: 9, num: 1, name: "강대현", email: "daehyeon9@.ai.cne.go.kr", pw: "!1q2w3e30r" },
      { grade: 2, classNum: 9, num: 7, name: "김은솔", email: "eunsol9@.ai.cne.go.kr", pw: "!1q2w3e31r" },
      { grade: 2, classNum: 9, num: 23, name: "유선주", email: "seonju9@.ai.cne.go.kr", pw: "!1q2w3e32r" },
      { grade: 2, classNum: 9, num: 25, name: "정다은", email: "daeun9@.ai.cne.go.kr", pw: "!1q2w3e33r" },
      { grade: 2, classNum: 9, num: 28, name: "채윤서", email: "yunseo9@.ai.cne.go.kr", pw: "!1q2w3e34r" },
      { grade: 2, classNum: 9, num: 30, name: "최완우", email: "wanu9@.ai.cne.go.kr", pw: "!1q2w3e35r" }
    ];

    let currentUsername = "";
    let currentEmail = "";
    let currentPassword = "";
    let isPasswordMasked = false;
    let qrGenerated = false;

    // 계정 조회 함수
    function searchStudent() {
      const classVal = document.getElementById('classSelect').value;
      const numVal = document.getElementById('numberInput').value.trim();
      const nameVal = document.getElementById('nameInput').value.trim();

      const resultCard = document.getElementById('resultCard');
      const errorBox = document.getElementById('errorBox');

      if (!classVal) {
        showError("반을 선택해 주세요.");
        return;
      }
      if (!numVal) {
        showError("번호를 입력해 주세요.");
        return;
      }

      const cNum = parseInt(classVal, 10);
      const nNum = parseInt(numVal, 10);

      // 반, 번호로 검색
      const found = studentData.find(s => s.classNum === cNum && s.num === nNum);

      if (!found) {
        showError(`2학년 ${cNum}반 ${nNum}번 학생의 정보가 없습니다. 번호를 다시 확인해 주세요.`);
        resultCard.classList.add('hidden');
        return;
      }

      // 이름 입력 시 교차 검증
      if (nameVal && found.name.replace(/\s+/g, '') !== nameVal.replace(/\s+/g, '')) {
        showError(`입력하신 이름('${nameVal}')이 2학년 ${cNum}반 ${nNum}번 학생명과 일치하지 않습니다.`);
        resultCard.classList.add('hidden');
        return;
      }

      // 조회 성공 UI 반영
      errorBox.classList.add('hidden');
      document.getElementById('resClassNum').textContent = `2학년 ${found.classNum}반 ${found.num}번`;
      document.getElementById('resName').textContent = `${found.name} 학생`;

      // @ 기준 아이디와 도메인 분리
      const parts = found.email.split('@');
      currentUsername = parts[0]; // @ 이전 아이디
      currentEmail = found.email;  // 전체 이메일
      currentPassword = found.pw;

      document.getElementById('resUsername').textContent = currentUsername;
      document.getElementById('resDomain').textContent = `@${parts[1] || '.ai.cne.go.kr'}`;

      isPasswordMasked = false;
      renderPassword();

      resultCard.classList.remove('hidden');
      showToast(`${found.name} 학생의 계정 정보를 확인했습니다.`);
    }

    // @ 이전 아이디만 복사하는 핵심 함수
    function copyOnlyUsername() {
      if (!currentUsername) return;
      copyToClipboard(currentUsername, `아이디 [ ${currentUsername} ] 가 복사되었습니다!`);
    }

    // 전체 이메일 복사 함수
    function copyFullEmail() {
      if (!currentEmail) return;
      copyToClipboard(currentEmail, "이메일 전체 주소가 복사되었습니다.");
    }

    // 비밀번호 복사 함수
    function copyPassword() {
      if (!currentPassword) return;
      copyToClipboard(currentPassword, "임시 비밀번호가 복사되었습니다!");
    }

    // 페이지 URL 복사 (학생 공유용)
    function copyShareUrl() {
      const currentUrl = window.location.href;
      copyToClipboard(currentUrl, "페이지 주소가 복사되었습니다! 학생들에게 전달해주세요.");
    }

    // 클립보드 복사 실행 (iFrame 안정 호환)
    function copyToClipboard(text, successMsg) {
      const tempInput = document.createElement('textarea');
      tempInput.value = text;
      tempInput.style.position = 'fixed';
      tempInput.style.opacity = '0';
      document.body.appendChild(tempInput);
      tempInput.focus();
      tempInput.select();
      try {
        document.execCommand('copy');
        showToast(successMsg || "클립보드에 복사되었습니다.");
      } catch (err) {
        showToast("복사 권한이 제한되어 있습니다. 화면의 텍스트를 직접 복사해 주세요.");
      }
      document.body.removeChild(tempInput);
    }

    // 비밀번호 가리기 토글
    function togglePasswordVisibility() {
      isPasswordMasked = !isPasswordMasked;
      renderPassword();
    }

    function renderPassword() {
      const pwEl = document.getElementById('resPw');
      const eyeIcon = document.getElementById('eyeIcon');
      if (isPasswordMasked) {
        pwEl.textContent = '••••••••••';
        eyeIcon.innerHTML = `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858.908a3 3 0 114.243 4.243M9.878 9.878l4.242 4.242M9.88 9.88l-3.29-3.29m7.532 7.532l3.29 3.29M3 3l18 18"></path>`;
      } else {
        pwEl.textContent = currentPassword;
        eyeIcon.innerHTML = `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"></path>`;
      }
    }

    function showError(msg) {
      const errorBox = document.getElementById('errorBox');
      const errorMsg = document.getElementById('errorMsg');
      errorMsg.textContent = msg;
      errorBox.classList.remove('hidden');
    }

    let toastTimer = null;
    function showToast(text) {
      const toast = document.getElementById('toast');
      const toastMsg = document.getElementById('toastMsg');
      toastMsg.textContent = text;
      toast.classList.remove('opacity-0', 'pointer-events-none');
      toast.classList.add('opacity-100');

      if (toastTimer) clearTimeout(toastTimer);
      toastTimer = setTimeout(() => {
        toast.classList.remove('opacity-100');
        toast.classList.add('opacity-0', 'pointer-events-none');
      }, 2500);
    }

    // QR코드 생성 및 모달 열기
    function openShareModal() {
      const shareModal = document.getElementById('shareModal');
      const qrcodeContainer = document.getElementById('qrcodeContainer');
      
      if (!qrGenerated && typeof QRCode !== 'undefined') {
        qrcodeContainer.innerHTML = "";
        new QRCode(qrcodeContainer, {
          text: window.location.href,
          width: 160,
          height: 160,
          colorDark : "#312e81",
          colorLight : "#ffffff",
          correctLevel : QRCode.CorrectLevel.M
        });
        qrGenerated = true;
      }
      shareModal.classList.remove('hidden');
    }

    // 전체 학생 표 렌더링
    function renderTable(list) {
      const tbody = document.getElementById('studentTableBody');
      tbody.innerHTML = '';
      list.forEach(student => {
        const tr = document.createElement('tr');
        tr.className = "hover:bg-slate-50 transition border-b border-slate-100";
        tr.innerHTML = `
          <td class="p-2.5 font-medium text-slate-700 whitespace-nowrap">2-${student.classNum} (${student.num}번)</td>
          <td class="p-2.5 font-bold text-slate-900 whitespace-nowrap">${student.name}</td>
          <td class="p-2.5 font-mono text-slate-600 truncate max-w-[140px] select-all">${student.email}</td>
          <td class="p-2.5 font-mono font-semibold text-indigo-600 select-all">${student.pw}</td>
        `;
        tbody.appendChild(tr);
      });
    }

    document.addEventListener('DOMContentLoaded', () => {
      document.getElementById('searchBtn').addEventListener('click', searchStudent);

      ['numberInput', 'nameInput'].forEach(id => {
        document.getElementById(id).addEventListener('keydown', (e) => {
          if (e.key === 'Enter') {
            e.preventDefault();
            searchStudent();
          }
        });
      });

      // 공유 모달 이벤트
      const shareModal = document.getElementById('shareModal');
      document.getElementById('sharePageBtn').addEventListener('click', openShareModal);
      document.getElementById('closeShareBtn').addEventListener('click', () => {
        shareModal.classList.add('hidden');
      });
      shareModal.addEventListener('click', (e) => {
        if (e.target === shareModal) shareModal.classList.add('hidden');
      });

      // 전체 명단 모달 이벤트
      const listModal = document.getElementById('fullListModal');
      document.getElementById('toggleListBtn').addEventListener('click', () => {
        renderTable(studentData);
        listModal.classList.remove('hidden');
      });

      document.getElementById('closeListBtn').addEventListener('click', () => {
        listModal.classList.add('hidden');
      });

      listModal.addEventListener('click', (e) => {
        if (e.target === listModal) listModal.classList.add('hidden');
      });

      // 전체 목록 검색 필터
      document.getElementById('tableFilterInput').addEventListener('input', (e) => {
        const query = e.target.value.trim().toLowerCase();
        const filtered = studentData.filter(s => 
          s.name.includes(query) || 
          `${s.classNum}반`.includes(query) || 
          `${s.classNum}-${s.num}`.includes(query) ||
          s.num.toString() === query ||
          s.email.toLowerCase().includes(query)
        );
        renderTable(filtered);
      });
    });
  </script>
</body>
</html>
