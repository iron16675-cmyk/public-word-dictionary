# public-word-dictionary<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>쉬운 우리말 공공언어 사전 & 변환기</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Font (Noto Sans KR) -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    
    <!-- PWA 앱 설정 연동 -->
    <link rel="manifest" href="manifest.json">
    <script>
        if ('serviceWorker' in navigator) {
            navigator.serviceWorker.register('sw.js');
        }
    </script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Noto Sans KR', 'sans-serif'],
                    },
                    colors: {
                        gov: {
                            50: '#f0f7ff',
                            100: '#e0effe',
                            500: '#0066ff',
                            600: '#0052cc',
                            700: '#003d99',
                            800: '#002966',
                            900: '#001433',
                        },
                        tealGov: {
                            500: '#00a896',
                            600: '#008073',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        .highlight-word {
            background-color: #fef08a;
            color: #854d0e;
            padding: 2px 6px;
            border-radius: 4px;
            font-weight: 600;
            border-bottom: 2px solid #eab308;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans min-h-screen flex flex-col antialiased">

    <!-- Header Section -->
    <header class="bg-gov-800 text-white shadow-md sticky top-0 z-50">
        <div class="max-w-6xl mx-auto px-4 py-3.5 flex justify-between items-center">
            <div class="flex items-center space-x-3 cursor-pointer" onclick="switchTab('converter')">
                <div class="w-10 h-10 rounded-xl bg-white/10 backdrop-blur flex items-center justify-center border border-white/20 shadow-inner">
                    <i data-lucide="languages" class="w-6 h-6 text-teal-300"></i>
                </div>
                <div>
                    <h1 class="text-xl font-bold tracking-tight flex items-center gap-2">
                        공공언어 사전 <span class="text-xs font-normal bg-teal-500/30 text-teal-200 border border-teal-400/30 px-2 py-0.5 rounded-full">쉬운 우리말</span>
                    </h1>
                    <p class="text-xs text-slate-300 hidden sm:block">어려운 행정·외래 용어를 알기 쉬운 우리말로</p>
                </div>
            </div>
            
            <div class="flex items-center gap-2">
                <button onclick="openModal('infoModal')" class="text-xs sm:text-sm bg-white/10 hover:bg-white/20 transition px-3 py-1.5 rounded-lg flex items-center gap-1.5 border border-white/10">
                    <i data-lucide="info" class="w-4 h-4 text-teal-300"></i>
                    <span>공공언어란?</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-1 max-w-6xl w-full mx-auto px-4 py-6 space-y-6">

        <!-- Navigation Tabs -->
        <div class="flex border-b border-slate-200 bg-white rounded-xl p-1.5 shadow-sm text-sm font-medium overflow-x-auto">
            <button id="tab-converter" onclick="switchTab('converter')" class="flex-1 min-w-[120px] py-2.5 rounded-lg flex items-center justify-center gap-2 transition-all bg-gov-600 text-white shadow-sm font-semibold">
                <i data-lucide="wand2" class="w-4 h-4"></i>
                <span>문장 순화 변환기</span>
            </button>
            <button id="tab-dictionary" onclick="switchTab('dictionary')" class="flex-1 min-w-[120px] py-2.5 rounded-lg flex items-center justify-center gap-2 transition-all text-slate-600 hover:text-slate-900 hover:bg-slate-100">
                <i data-lucide="book-open" class="w-4 h-4"></i>
                <span>순화어 사전 검색</span>
            </button>
            <button id="tab-examples" onclick="switchTab('examples')" class="flex-1 min-w-[120px] py-2.5 rounded-lg flex items-center justify-center gap-2 transition-all text-slate-600 hover:text-slate-900 hover:bg-slate-100">
                <i data-lucide="file-check" class="w-4 h-4"></i>
                <span>실제 순화 사례</span>
            </button>
        </div>

        <!-- TAB 1: CONVERTER (문장 순화 변환기) -->
        <section id="sec-converter" class="space-y-6">
            <div class="bg-blue-50 border border-blue-200 rounded-xl p-4 flex items-start gap-3">
                <i data-lucide="sparkles" class="w-5 h-5 text-gov-600 shrink-0 mt-0.5"></i>
                <div class="text-sm text-slate-700">
                    <span class="font-bold text-gov-700">공문서, 보도자료, 안내문</span> 속 어려운 외국어나 특수 행정 용어를 입력해보세요. 국립국어원 표준 기준에 맞춰 쉬운 우리말로 순화합니다.
                </div>
            </div>

            <!-- Two Column Layout: Original & Simplified -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <!-- Input Box -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between items-center mb-3">
                            <label for="inputText" class="font-semibold text-slate-800 flex items-center gap-2">
                                <i data-lucide="edit-3" class="w-4 h-4 text-gov-600"></i>
                                원문 입력 (어려운 공공언어 포함)
                            </label>
                            <button onclick="clearInput()" class="text-xs text-slate-400 hover:text-slate-600 flex items-center gap-1">
                                <i data-lucide="rotate-ccw" class="w-3 h-3"></i> 지우기
                            </button>
                        </div>
                        <textarea id="inputText" rows="7" 
                            class="w-full p-3.5 border border-slate-200 rounded-xl focus:ring-2 focus:ring-gov-500 focus:border-gov-500 text-slate-800 placeholder-slate-400 text-base leading-relaxed resize-none outline-none transition"
                            placeholder="예: 이번 프로젝트의 니즈를 파악하기 위해 TF팀을 구성하고 모빌리티 분야 ESG 경영 확산을 위해 심포지엄을 개최하겠습니다."></textarea>
                    </div>

                    <!-- Preset Samples -->
                    <div class="mt-4 pt-3 border-t border-slate-100">
                        <p class="text-xs text-slate-500 mb-2 font-medium">💡 예시문 클릭해서 시험해보기:</p>
                        <div class="flex flex-wrap gap-1.5">
                            <button onclick="setSample(1)" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-md transition">
                                #1 보도자료 예시
                            </button>
                            <button onclick="setSample(2)" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-md transition">
                                #2 안내문 예시
                            </button>
                            <button onclick="setSample(3)" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-md transition">
                                #3 사업 계획 예시
                            </button>
                        </div>
                    </div>

                    <button onclick="convertText()" class="mt-4 w-full bg-gov-600 hover:bg-gov-700 text-white font-semibold py-3 rounded-xl shadow hover:shadow-md transition flex items-center justify-center gap-2">
                        <i data-lucide="arrow-right-left" class="w-5 h-5"></i>
                        <span>쉬운 우리말로 순화하기</span>
                    </button>
                </div>

                <!-- Output Box -->
                <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 flex flex-col justify-between">
                    <div>
                        <div class="flex justify-between items-center mb-3">
                            <h2 class="font-semibold text-slate-800 flex items-center gap-2">
                                <i data-lucide="check-circle2" class="w-4 h-4 text-emerald-600"></i>
                                순화된 우리말 문장
                            </h2>
                            <button onclick="copyOutput()" id="copyBtn" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-2.5 py-1 rounded-md transition flex items-center gap-1">
                                <i data-lucide="copy" class="w-3.5 h-3.5"></i>
                                <span>복사하기</span>
                            </button>
                        </div>
                        <div id="outputText" class="w-full min-h-[168px] p-3.5 bg-slate-50 border border-slate-200 rounded-xl text-slate-800 text-base leading-relaxed overflow-y-auto">
                            <span class="text-slate-400 italic">왼쪽 창에 글을 입력한 후 [순화하기] 버튼을 누르시면, 변환된 결과와 변경 단어가 표시됩니다.</span>
                        </div>
                    </div>

                    <!-- Statistics / Replaced Summary -->
                    <div class="mt-4 pt-3 border-t border-slate-100">
                        <div id="convertedWordsSummary" class="text-xs text-slate-600">
                            <span class="font-medium text-slate-500">순화 내역:</span> <span class="text-slate-400 italic">변환된 단어가 없습니다.</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Quick Replacement Tags Grid -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5">
                <h3 class="text-sm font-bold text-slate-800 mb-3 flex items-center gap-2">
                    <i data-lucide="zap" class="w-4 h-4 text-amber-500"></i>
                    주요 공공언어 빠른 순화 테스트
                </h3>
                <div id="quickTags" class="flex flex-wrap gap-2 text-xs">
                    <!-- Dynamic rendering -->
                </div>
            </div>
        </section>

        <!-- TAB 2: DICTIONARY VIEW (사전 검색) -->
        <section id="sec-dictionary" class="space-y-6 hidden">
            <!-- Search & Filters -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 space-y-4">
                <div class="flex flex-col sm:flex-row gap-3">
                    <div class="relative flex-1">
                        <i data-lucide="search" class="w-5 h-5 text-slate-400 absolute left-3.5 top-3.5"></i>
                        <input type="text" id="dictSearchInput" oninput="renderDictionary()" 
                            placeholder="어려운 단어(예: ESG, 모빌리티, 디깅위크, MOU, 키오스크)나 순화어를 검색하세요..." 
                            class="w-full pl-11 pr-10 py-3 border border-slate-200 rounded-xl focus:ring-2 focus:ring-gov-500 focus:border-gov-500 text-slate-800 outline-none text-base">
                        <button onclick="clearDictSearch()" id="dictClearBtn" class="hidden absolute right-3.5 top-3.5 text-slate-400 hover:text-slate-600">
                            <i data-lucide="x" class="w-5 h-5"></i>
                        </button>
                    </div>
                    <button onclick="openModal('addWordModal')" class="bg-tealGov-500 hover:bg-tealGov-600 text-white px-4 py-3 rounded-xl font-semibold text-sm flex items-center justify-center gap-2 transition shrink-0">
                        <i data-lucide="plus-circle" class="w-4 h-4"></i>
                        <span>사용자 용어 추가</span>
                    </button>
                </div>

                <!-- Category Chips -->
                <div class="flex flex-wrap gap-2 text-xs font-medium">
                    <button onclick="setCategory('all')" id="cat-all" class="cat-btn px-3 py-1.5 rounded-full bg-gov-600 text-white font-semibold">전체 보기</button>
                    <button onclick="setCategory('최신/신조어')" id="cat-최신/신조어" class="cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200">최신/신조어</button>
                    <button onclick="setCategory('행정/공공용어')" id="cat-행정/공공용어" class="cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200">행정/공공용어</button>
                    <button onclick="setCategory('외래어/외국어')" id="cat-외래어/외국어" class="cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200">외래어/외국어</button>
                    <button onclick="setCategory('전문/IT용어')" id="cat-전문/IT용어" class="cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200">전문/IT용어</button>
                    <button onclick="setCategory('사용자 추가')" id="cat-사용자 추가" class="cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200">사용자 추가 단어</button>
                </div>
            </div>

            <!-- Result Count -->
            <div class="flex justify-between items-center text-xs text-slate-500 px-1">
                <span>총 <strong id="dictResultCount" class="text-gov-700 font-bold">0</strong>개의 용어가 등록되어 있습니다.</span>
                <span class="text-slate-400">자료 출처: 국립국어원 쉬운 우리말 쓰기</span>
            </div>

            <!-- Dictionary Grid -->
            <div id="dictionaryGrid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- TAB 3: EXAMPLES (실제 문장 사례) -->
        <section id="sec-examples" class="space-y-6 hidden">
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5">
                <h3 class="text-base font-bold text-slate-800 mb-1">공문서·보도자료 실제 순화 사례</h3>
                <p class="text-xs text-slate-500 mb-4">공공기관에서 실제 쓰이는 문장을 어떻게 쉽고 정확하게 바꿀 수 있는지 확인해보세요.</p>
                <div id="exampleSentenceList" class="space-y-4">
                    <!-- Dynamic Examples -->
                </div>
            </div>
        </section>
    </main>

    <!-- Modal: Add Custom Word -->
    <div id="addWordModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-2xl shadow-xl max-w-md w-full p-6 space-y-4">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 text-lg flex items-center gap-2">
                    <i data-lucide="plus-circle" class="w-5 h-5 text-tealGov-500"></i>
                    새로운 순화 용어 등록
                </h3>
                <button onclick="closeModal('addWordModal')" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>
            <div class="space-y-3 text-sm">
                <div>
                    <label class="block font-semibold text-slate-700 mb-1">어려운 공공언어 / 외국어</label>
                    <input type="text" id="newOrig" placeholder="예: 숏폼" class="w-full p-2.5 border border-slate-200 rounded-lg focus:ring-2 focus:ring-gov-500 outline-none">
                </div>
                <div>
                    <label class="block font-semibold text-slate-700 mb-1">순화어 (쉬운 우리말)</label>
                    <input type="text" id="newTarget" placeholder="예: 요약 영상" class="w-full p-2.5 border border-slate-200 rounded-lg focus:ring-2 focus:ring-gov-500 outline-none">
                </div>
                <div>
                    <label class="block font-semibold text-slate-700 mb-1">카테고리</label>
                    <select id="newCategory" class="w-full p-2.5 border border-slate-200 rounded-lg focus:ring-2 focus:ring-gov-500 outline-none bg-white">
                        <option value="최신/신조어">최신/신조어</option>
                        <option value="행정/공공용어">행정/공공용어</option>
                        <option value="외래어/외국어">외래어/외국어</option>
                        <option value="전문/IT용어">전문/IT용어</option>
                    </select>
                </div>
                <div>
                    <label class="block font-semibold text-slate-700 mb-1">용어 설명</label>
                    <input type="text" id="newDesc" placeholder="단어의 의미나 용도를 간단히 적어주세요." class="w-full p-2.5 border border-slate-200 rounded-lg focus:ring-2 focus:ring-gov-500 outline-none">
                </div>
            </div>
            <div class="pt-2 flex gap-2 justify-end">
                <button onclick="closeModal('addWordModal')" class="px-4 py-2 text-slate-600 hover:bg-slate-100 rounded-lg transition text-sm">취소</button>
                <button onclick="addUserWord()" class="bg-tealGov-500 hover:bg-tealGov-600 text-white px-4 py-2 rounded-lg font-medium transition text-sm">등록하기</button>
            </div>
        </div>
    </div>

    <!-- Modal: Info -->
    <div id="infoModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-2xl shadow-xl max-w-lg w-full p-6 space-y-4">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 text-lg flex items-center gap-2">
                    <i data-lucide="bookmark" class="w-5 h-5 text-gov-600"></i>
                    쉬운 공공언어 바르게 알기
                </h3>
                <button onclick="closeModal('infoModal')" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>
            <div class="text-sm text-slate-600 leading-relaxed space-y-3">
                <p><strong class="text-slate-800">공공언어란?</strong><br>정부 및 공공기관이 국민을 대상으로 작성하는 공문서, 보도자료, 안내문, 법령 등에 사용하는 언어입니다.</p>
                <p><strong class="text-slate-800">왜 쉬운 우리말로 바꿔야 할까요?</strong><br>어려운 한자어나 불필요한 외국어·외래어 대신 쉬운 우리말을 사용하면 모든 국민이 정보 격차 없이 공공 정책을 쉽게 이해할 수 있습니다.</p>
                <p class="bg-slate-50 p-3 rounded-lg border border-slate-200 text-xs text-slate-500">본 앱에 수록된 순화어는 <strong>국립국어원 '쉬운 우리말 쓰기'</strong> 기준 및 최근 다듬은 말 가이드라인을 참조하여 제작되었습니다.</p>
            </div>
            <div class="pt-2 text-right">
                <button onclick="closeModal('infoModal')" class="bg-gov-600 hover:bg-gov-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition">확인</button>
            </div>
        </div>
    </div>

    <!-- Notification Toast -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-800 text-white px-4 py-3 rounded-xl shadow-lg text-sm flex items-center gap-2 transform translate-y-20 opacity-0 transition-all duration-300 z-50">
        <i data-lucide="check-circle" class="w-4 h-4 text-emerald-400"></i>
        <span id="toastMsg">메시지</span>
    </div>

    <!-- Footer -->
    <footer class="bg-slate-100 border-t border-slate-200 mt-auto py-6">
        <div class="max-w-6xl mx-auto px-4 text-center text-xs text-slate-500 space-y-1">
            <p class="font-medium text-slate-600">공공언어 사전 & 쉬운 우리말 변환 웹 앱</p>
            <p>국민 모두가 이해하기 쉬운 알기 쉬운 공공언어 사용을 응원합니다.</p>
        </div>
    </footer>

    <!-- JavaScript Application Logic -->
    <script>
        // ==========================================
        // DATASET: Extensive Public Words Dictionary (Image Data & National Institute of Korean Language)
        // ==========================================
        const dictionaryData = [
            // Image 1, 2, 3: 경기도 공공언어 개선대상 목록 & 국립국어원 필수 개선 행정용어
            { id: 301, original: "개소", target: "군데 / 곳", category: "행정/공공용어", desc: "수량을 세는 한자어 단위", example: "복지관 3개소 개관. → 복지관 3곳 개관." },
            { id: 302, original: "계상", target: "반영 / 계산하여 넒", category: "행정/공공용어", desc: "예산 등에 계산하여 올림", example: "사업비 예산 계상. → 사업비 예산 반영." },
            { id: 303, original: "공구", target: "공사구역", category: "행정/공공용어", desc: "공사를 나누어 진행하는 구역", example: "제1공구 공사 착수. → 제1공사구역 공사 시작." },
            { id: 304, original: "익일", target: "다음 날", category: "행정/공공용어", desc: "어느 날 뒤에 오는 날", example: "신청 익일 발송. → 신청 다음 날 발송." },
            { id: 305, original: "저감", target: "낮춤 / 줄임", category: "행정/공공용어", desc: "양이나 수치를 낮추거나 줄임", example: "미세먼지 저감 대책. → 미세먼지 줄임 대책." },
            { id: 306, original: "당초", target: "애초 / 맨 처음", category: "행정/공공용어", desc: "일이 시작된 맨 처음", example: "당초 계획 변경. → 애초(맨 처음) 계획 변경." },
            { id: 307, original: "동법", target: "같은 법", category: "행정/공공용어", desc: "앞에서 언급된 법률", example: "동법 제3조에 따라. → 같은 법 제3조에 따라." },
            { id: 308, original: "제호", target: "제목", category: "행정/공공용어", desc: "책이나 문서의 이름", example: "조례 제호 변경. → 조례 제목 변경." },
            { id: 309, original: "조기", target: "빠른 시일", category: "행정/공공용어", desc: "일찍 또는 빠른 때", example: "사업 조기 완공. → 사업 빠른 시일 완공." },
            { id: 310, original: "차순위", target: "다음 차례 / 다음 순위", category: "행정/공공용어", desc: "바로 다음 차례", example: "차순위 자 선정. → 다음 순위 자 선정." },
            { id: 311, original: "요망", target: "바람", category: "행정/공공용어", desc: "바라거나 요구함", example: "적극 참여 요망. → 적극 참여 바람." },
            { id: 312, original: "유관기관", target: "관계기관", category: "행정/공공용어", desc: "관계가 있는 기관", example: "유관기관 협조 요청. → 관계기관 협조 요청." },
            { id: 313, original: "관급", target: "관공서 지급", category: "행정/공공용어", desc: "관청에서 지급함", example: "관급 자재 구매. → 관공서 지급 자재 구매." },
            { id: 314, original: "금회", target: "이번", category: "행정/공공용어", desc: "지금 이번 차례", example: "금회 공모 사업. → 이번 공모 사업." },
            { id: 315, original: "적기", target: "제때", category: "행정/공공용어", desc: "적당한 시기", example: "적기 지원 추진. → 제때 지원 추진." },
            { id: 316, original: "제고", target: "높임 / 높이기", category: "행정/공공용어", desc: "수준이나 정도를 높임", example: "도시 경쟁력 제고. → 도시 경쟁력 높이기." },
            { id: 317, original: "제반", target: "여러 / 모든", category: "행정/공공용어", desc: "어떤 것과 관련된 온갖 것", example: "제반 사항 점검. → 여러(모든) 사항 점검." },
            { id: 318, original: "득하다", target: "받다 / 얻다", category: "행정/공공용어", desc: "허가나 승인을 받음", example: "허가를 득한 후. → 허가를 받은 후." },
            { id: 319, original: "조선족", target: "중국 동포", category: "행정/공공용어", desc: "중국 국적을 가진 동포", example: "조선족 지원 사업. → 중국 동포 지원 사업." },
            { id: 320, original: "미망인", target: "고 OOO씨의 부인", category: "행정/공공용어", desc: "남편이 죽고 홀로 남은 여성을 높여 부르던 말 (권장대체)", example: "순직 공무원 미망인. → 순직 공무원 고 OOO씨의 부인." },
            { id: 321, original: "리스크", target: "위험 / 손실 우려", category: "외래어/외국어", desc: "위험 요소나 손실의 가능성", example: "투자 리스크 관리. → 투자 위험 관리." },
            { id: 322, original: "마스터플랜", target: "종합계획", category: "외래어/외국어", desc: "전체적인 추진 방향을 담은 계획", example: "도시 재개발 마스터플랜. → 도시 재개발 종합계획." },
            { id: 323, original: "아웃리치", target: "현장지원활동", category: "외래어/외국어", desc: "직접 현장에 찾아가 제공하는 지원", example: "청소년 아웃리치 상담. → 청소년 현장지원활동 상담." },
            { id: 324, original: "아카이브", target: "자료보관소", category: "외래어/외국어", desc: "기록 및 정보 자료 보관 시스템", example: "시정 아카이브 구축. → 시정 자료보관소 구축." },
            { id: 325, original: "ICT", target: "정보통신기술", category: "전문/IT용어", desc: "정보 기술과 통신 기술의 합성어", example: "ICT 융합 산업. → 정보통신기술 융합 산업." },
            { id: 326, original: "매뉴얼", target: "안내서 / 설명서", category: "외래어/외국어", desc: "작동 방법이나 절차를 적은 책자", example: "재난 대응 매뉴얼. → 재난 대응 안내서." },
            { id: 327, original: "브로슈어", target: "소책자", category: "외래어/외국어", desc: "안내나 홍보를 목적으로 만든 소책자", example: "홍보 브로슈어 제작. → 홍보 소책자 제작." },
            { id: 328, original: "IR", target: "기업설명회", category: "전문/IT용어", desc: "투자자들을 대상으로 하는 기업 설명회", example: "스타트업 IR 개최. → 새싹기업 기업설명회 개최." },
            { id: 329, original: "어젠다", target: "의제", category: "행정/공공용어", desc: "회의나 회의체에서 논의할 주요 과제", example: "핵심 어젠다 선정. → 핵심 의제 선정." },
            { id: 330, original: "O2O", target: "온오프라인 연계사업", category: "전문/IT용어", desc: "온라인과 오프라인을 연결하는 서비스", example: "O2O 플랫폼 구축. → 온오프라인 연계 플랫폼 구축." },
            { id: 331, original: "결손가족", target: "한부모가족 / 조손가족", category: "행정/공공용어", desc: "차별적 어감이 포함된 용어 차단", example: "결손가족 지원. → 한부모가족 지원." },
            { id: 332, original: "착수", target: "시작", category: "행정/공공용어", desc: "어떤 일에 손을 댐", example: "공사 착수. → 공사 시작." },
            { id: 333, original: "총연장", target: "총길이", category: "행정/공공용어", desc: "전체 전체의 길이를 말함", example: "도로 총연장 10km. → 도로 총길이 10km." },
            { id: 334, original: "취하", target: "취소 / 철회", category: "행정/공공용어", desc: "신청이나 소송을 거두어들임", example: "고소 취하. → 고소 취소(철회)." },
            { id: 335, original: "퍼스널 모빌리티", target: "1인 전동차", category: "최신/신조어", desc: "전동 킥보드 등 1인용 이동수단", example: "퍼스널 모빌리티 안전 규칙. → 1인 전동차 안전 규칙." },
            { id: 336, original: "프레젠테이션", target: "설명 / 설명회", category: "외래어/외국어", desc: "시각 자료를 활용한 발표", example: "사업 프레젠테이션 진행. → 사업 설명회 진행." },
            { id: 337, original: "필하다", target: "마치다 / 끝내다", category: "행정/공공용어", desc: "필요한 절차를 완료함", example: "등록을 필한 자. → 등록을 마친 자." },
            { id: 338, original: "학부형", target: "학부모", category: "행정/공공용어", desc: "학생의 보호자", example: "학부형 총회. → 학부모 총회." },
            { id: 339, original: "업로드", target: "올리기", category: "전문/IT용어", desc: "파일이나 자료를 인터넷에 게시함", example: "파일 업로드. → 파일 올리기." },
            { id: 340, original: "다운로드", target: "내려받기", category: "전문/IT용어", desc: "인포메이션이나 파일 수신", example: "양식 다운로드. → 양식 내려받기." },
            { id: 341, original: "인포그래픽", target: "정보 그림", category: "전문/IT용어", desc: "정보를 직관적으로 표현한 시각 자료", example: "인포그래픽 제작. → 정보 그림 제작." },
            { id: 342, original: "규제 샌드박스", target: "규제 유예(제도)", category: "행정/공공용어", desc: "신기술 사업에 규제를 유예하는 제도", example: "규제 샌드박스 적용. → 규제 유예 적용." },
            { id: 343, original: "컨설팅", target: "조언 / 상담", category: "행정/공공용어", desc: "전문적인 지식으로 도움을 줌", example: "경영 컨설팅. → 경영 조언(상담)." },
            { id: 344, original: "케어", target: "돌봄", category: "외래어/외국어", desc: "보살핌이나 간호", example: "어르신 케어 서비스. → 어르신 돌봄 서비스." },
            { id: 345, original: "홈페이지", target: "누리집", category: "외래어/외국어", desc: "인터넷 웹사이트", example: "시청 홈페이지. → 시청 누리집." },

            // User requested latest terms & Newly Added Public Words
            { id: 201, original: "언택트", target: "비대면", category: "외래어/외국어", desc: "접촉하지 않고 이루어지는 대면 최소화 서비스나 방식", example: "언택트 민원 서비스 도입. → 비대면 민원 서비스 도입." },
            { id: 202, original: "모니터링", target: "점검 / 관찰 / 실태 조사", category: "행정/공공용어", desc: "상황이나 진행 과정을 지속적으로 살펴보고 체계적으로 점검함", example: "사업 진행상황 모니터링 실시. → 사업 진행상황 점검 실시." },
            { id: 203, original: "매칭펀드", target: "매칭 자금 / 대응 자금", category: "행정/공공용어", desc: "공공기관과 민간이 일정 비율로 나누어 함께 투자·출자하는 펀드", example: "시비 매칭펀드 조성. → 시비 대응 자금 조성." },
            { id: 204, original: "소셜벤처", target: "사회적 도전 기업", category: "최신/신조어", desc: "사회적 문제를 해결하기 위해 혁신적 기술이나 사업 모델을 활용하는 기업", example: "지역 소셜벤처 육성 사업. → 지역 사회적 도전 기업 육성 사업." },
            { id: 205, original: "인센티브", target: "성과급 / 유인책 / 포상혜택", category: "행정/공공용어", desc: "목표 달성을 장려하기 위해 제공하는 혜택이나 보상", example: "우수 자치구 인센티브 제공. → 우수 자치구 포상혜택 제공." },
            { id: 206, original: "테스트베드", target: "실증 공간 / 실습장", category: "전문/IT용어", desc: "새로운 기술이나 제품의 성능과 효과를 시험 검증하기 위한 환경", example: "스마트시티 테스트베드 구축. → 지능형 도시 실증 공간 구축." },
            { id: 207, original: "라운드테이블", target: "원탁 회의 / 원탁 토론", category: "행정/공공용어", desc: "참가자들이 대등한 자격으로 자유롭게 의견을 나누는 토론 모임", example: "시민 참여 라운드테이블. → 시민 참여 원탁 토론." },
            { id: 208, original: "스마트빌리지", target: "지능형 마을", category: "최신/신조어", desc: "지능정보기술을 적용하여 농어촌의 주거 및 생활 환경을 개선한 마을", example: "스마트빌리지 조성 사업. → 지능형 마을 조성 사업." },
            { id: 209, original: "스마트인프라", target: "지능형 기반 시설", category: "행정/공공용어", desc: "첨단 정보통신 기술을 더해 효율적으로 운영되는 기반 시설", example: "도시 스마트인프라 확장. → 도시 지능형 기반 시설 확장." },
            { id: 210, original: "빅데이터", target: "대용량 정보", category: "전문/IT용어", desc: "기존 방식을 넘어선 방대하고 복잡한 수치 및 문자 데이터의 집합", example: "빅데이터 분석을 통한 정책 수립. → 대용량 정보 분석을 통한 정책 수립." },
            { id: 211, original: "거버넌스", target: "민관 협력 / 공공 경영", category: "행정/공공용어", desc: "정부, 기업, 시민사회 등이 함께 참여하는 협력적 국정 운영 방식", example: "지속가능한 거버넌스를 구축합니다. → 지속가능한 민관 협력체계를 구축합니다." },
            { id: 212, original: "마일리지", target: "적립금 / 이용 실적 점수", category: "외래어/외국어", desc: "서비스 이용 실적에 따라 차곡차곡 쌓여 혜택으로 환원되는 점수", example: "탄소포인트 마일리지 지급. → 탄소포인트 적립금 지급." },
            { id: 213, original: "거점센터", target: "중심 센터 / 핵심 거점", category: "행정/공공용어", desc: "특정 지역이나 분야의 서비스 및 업무를 총괄하는 중심 기관", example: "지역 돌봄 거점센터 개소. → 지역 돌봄 중심 센터 개소." },

            { id: 101, original: "ESG", target: "친환경·사회·투명 경영", category: "최신/신조어", desc: "환경 보호(E), 사회적 책임(S), 지배구조 개선(G)을 고려하는 지속가능한 경영 방식", example: "ESG 경영 실천을 위한 시정 발표. → 친환경·사회·투명 경영 실천을 위한 시정 발표." },
            { id: 102, original: "심포지엄", target: "학술 토론회", category: "행정/공공용어", desc: "특정 주제를 가지고 전문가들이 의견을 발표하고 대화하는 모임", example: "미래 도시 발전 심포지엄 개최. → 미래 도시 발전 학술 토론회 개최." },
            { id: 103, original: "벤처사업", target: "모험 기업 사업", category: "최신/신조어", desc: "창의성과 신기술을 바탕으로 위험을 무릅쓰고 도전하는 사업", example: "청년 벤처사업 육성 자금 지원. → 청년 모험 기업 사업 육성 자금 지원." },
            { id: 104, original: "디깅위크", target: "취향 탐구 주간", category: "최신/신조어", desc: "특정 주제나 과제에 일정 기간 동안 깊이 있게 몰입하여 탐구하는 기간", example: "이번 주를 디깅위크로 운영합니다. → 이번 주를 취향 탐구 주간으로 운영합니다." },
            { id: 105, original: "하이브리드", target: "혼합형", category: "최신/신조어", desc: "두 개 이상의 요소나 방식이 결합하여 이루어진 형태", example: "하이브리드 근무제 도입. → 혼합형 근무제 도입." },
            { id: 106, original: "기후테크컨페어", target: "기후 기술 박람회", category: "최신/신조어", desc: "기후 위기 대응 기술과 탄소중립 관련 제품을 전시하고 교류하는 종합 행사", example: "기후테크컨페어 참가를 신청하세요. → 기후 기술 박람회 참가를 신청하세요." },
            { id: 107, original: "미디어 리터러시", target: "매체 이해력", category: "최신/신조어", desc: "다양한 매체에서 제공하는 정보를 주체적으로 비판하고 올바르게 평가·활용하는 능력", example: "청소년 미디어 리터러시 교육 강화. → 청소년 매체 이해력 교육 강화." },
            { id: 108, original: "콘퍼런스", target: "학술 회의", category: "행정/공공용어", desc: "특정 분야의 전문적 주제에 대해 논의하거나 발표하는 대규모 회의", example: "글로벌 스마트시티 콘퍼런스. → 세계 지능형 도시 학술 회의." },
            { id: 109, original: "캐피털비즈니스", target: "금융 투자업", category: "최신/신조어", desc: "금융 자본의 투자와 유치를 기반으로 수익을 창출하는 사업", example: "지역 캐피털비즈니스 활성화. → 지역 금융 투자업 활성화." },
            { id: 110, original: "바이오헬스 첨단클러스터", target: "생명 건강 첨단 단지", category: "최신/신조어", desc: "생명과학, 의약품, 의료기기 관련 첨단 기업 및 연구소가 한곳에 모인 집적지", example: "바이오헬스 첨단클러스터 조성 사업. → 생명 건강 첨단 단지 조성 사업." },
            { id: 111, original: "모빌리티", target: "이동 수단", category: "최신/신조어", desc: "자율주행, 전동킥보드, 도심항공 등 첨단 기술이 적용된 차세대 교통 및 이동 수단", example: "미래 모빌리티 혁신 특구 지정. → 미래 이동 수단 혁신 특구 지정." },
            { id: 112, original: "이노베이션포럼", target: "혁신 토론회", category: "행정/공공용어", desc: "변화와 개혁을 이끌어내기 위해 전문가와 관계자들이 모여 의견을 나누는 토론회", example: "공공 서비스 이노베이션포럼. → 공공 서비스 혁신 토론회." },
            { id: 113, original: "네트워킹행사", target: "교류 행사", category: "외래어/외국어", desc: "참석자들이 상호 친목을 도모하고 정보 및 협력 체계를 구축하는 자리", example: "스타트업 대표자 네트워킹행사. → 신생 벤처기업 대표자 교류 행사." },

            // National Institute of Korean Language Public Standard Terms
            { id: 1, original: "니즈", target: "필요 / 요구", category: "외래어/외국어", desc: "소비자나 국민이 원하는 바 또는 필요 사항", example: "시민들의 니즈를 적극 수용하겠습니다. → 시민들의 필요(요구)를 적극 수용하겠습니다." },
            { id: 2, original: "리플렛", target: "홍보 전단 / 안내서", category: "외래어/외국어", desc: "손에 들고 볼 수 있는 소책자나 안내 인쇄물", example: "행사 안내 리플렛을 배포합니다. → 행사 안내서(홍보 전단)를 배포합니다." },
            { id: 3, original: "TF팀", target: "전담팀 / 특별팀", category: "전문/IT용어", desc: "특정 과제를 해결하기 위해 한시적으로 구성된 조직 (Task Force)", example: "비상대책 TF팀을 구성했습니다. → 비상대책 전담팀을 구성했습니다." },
            { id: 5, original: "인프라", target: "기반 시설", category: "행정/공공용어", desc: "경제 활동이나 사회 생활의 기반이 되는 도로, 통신, 시설 등", example: "디지털 인프라 구축사업. → 디지털 기반 시설 구축사업." },
            { id: 6, original: "싱크탱크", target: "정책 연구소", category: "행정/공공용어", desc: "각 분야 전문가들이 모여 연구 및 정책 개발을 하는 기구", example: "도시발전을 위한 싱크탱크 출범. → 도시발전을 위한 정책 연구소 출범." },
            { id: 7, original: "벤치마킹", target: "비교 평가 / 본보기 모방", category: "전문/IT용어", desc: "우수한 모범 사례를 배워서 자신의 방식을 개선하는 일", example: "해외 우수 사례 벤치마킹. → 해외 우수 사례 본보기 모방." },
            { id: 8, original: "커뮤니티", target: "지역 사회 / 공동체", category: "외래어/외국어", desc: "공통의 관심사나 지역을 공유하는 사람들의 모임", example: "지역 커뮤니티 활성화 사업. → 지역 공동체 활성화 사업." },
            { id: 9, original: "시그니처", target: "대표 / 상징", category: "외래어/외국어", desc: "특정 기관이나 지역을 대표하는 고유한 특징이나 상품", example: "우리 시의 시그니처 사업. → 우리 시의 대표 사업." },
            { id: 10, original: "원스톱", target: "일괄 / 한곳 처리", category: "행정/공공용어", desc: "여러 절차를 한 장소나 한 번에 처리하는 방식", example: "원스톱 민원 서비스 제공. → 일괄 민원 서비스 제공." },
            { id: 11, original: "MOU", target: "업무 협약", category: "행정/공공용어", desc: "기관 간 상호 협력을 위해 체결하는 양해각서", example: "기술 교류 MOU 체결. → 기술 교류 업무 협약 체결." },
            { id: 12, original: "릴레이", target: "연속 / 이어가기", category: "외래어/외국어", desc: "여러 사람이 차례로 이어가며 진행하는 행사나 행동", example: "릴레이 기부 캠페인. → 이어가기 기부 운동." },
            { id: 13, original: "팝업스토어", target: "임시 매장", category: "외래어/외국어", desc: "짧은 기간 동안 한시적으로 운영하는 매장", example: "청년창업 팝업스토어 오픈. → 청년창업 임시 매장 개장." },
            { id: 15, original: "플랫폼", target: "누리집 / 기반 시스템", category: "전문/IT용어", desc: "서비스가 이루어지는 기초 시스템이나 공간", example: "시민 참여형 온라인 플랫폼. → 시민 참여형 온라인 누리집." },
            { id: 16, original: "키오스크", target: "무인 단말기", category: "외래어/외국어", desc: "공공장소나 매장에 설치된 터치스크린 방식의 무인 정보안내 장치", example: "키오스크 사용법 교육. → 무인 단말기 사용법 교육." },
            { id: 17, original: "시니어", target: "어르신 / 장년", category: "외래어/외국어", desc: "나이가 많은 연장자나 고령층", example: "시니어 일자리 창출 사업. → 어르신 일자리 창출 사업." },
            { id: 18, original: "워라밸", target: "일·생활 균형", category: "최신/신조어", desc: "일(Work)과 삶(Life)의 균형을 뜻함", example: "직원들의 워라밸을 적극 지원합니다. → 직원들의 일·생활 균형을 적극 지원합니다." },
            { id: 19, original: "사이드 프로젝트", target: "부수 사업 / 별도 사업", category: "최신/신조어", desc: "본업 외에 부수적으로 진행하는 과제나 사업", example: "청년 사이드 프로젝트 공모. → 청년 부수 사업 공모." },
            { id: 20, original: "킬 스위치", target: "긴급 정지 장치", category: "전문/IT용어", desc: "비상 상황 시 시스템이나 장치를 즉시 끄는 차단 장치", example: "전동 킥보드 킬 스위치 의무화. → 전동 킥보드 긴급 정지 장치 의무화." },
            { id: 21, original: "스마트시티", target: "지능형 도시", category: "행정/공공용어", desc: "첨단 정보통신기술을 이용하여 도시 문제를 해결하는 도시", example: "스마트시티 건설 추진. → 지능형 도시 건설 추진." },
            { id: 22, original: "블라인드 테스트", target: "블라인드 평가 / 가림 평가", category: "외래어/외국어", desc: "대상 정보를 가린 채 평가하는 방식", example: "공정 심사를 위한 블라인드 테스트. → 공정 심사를 위한 블라인드 평가." },
            { id: 23, original: "워크숍", target: "공동 연수 / 연구회", category: "외래어/외국어", desc: "연구나 집단 작업을 하기 위해 모이는 토론회", example: "상반기 직원 워크숍 실시. → 상반기 직원 공동 연수 실시." },
            { id: 24, original: "스마트팜", target: "지능형 농장", category: "행정/공공용어", desc: "정보통신기술을 활용해 자동 관리되는 농장", example: "청년 스마트팜 보급 확대. → 청년 지능형 농장 보급 확대." },
            { id: 25, original: "스타트업", target: "신생 벤처기업 / 새싹기업", category: "최신/신조어", desc: "기술이나 아이디어를 바탕으로 창업한 초기 기업", example: "스타트업 투자 유치 설명회. → 새싹기업 투자 유치 설명회." }
        ];
let activeCategory = 'all';

        // Sample sentences with new words included
        const sampleSentences = {
            1: "이번 바이오헬스 첨단클러스터 및 스마트빌리지 사업과 관련하여 언택트 민원 서비스 모니터링을 진행하고, 소셜벤처 지원을 위해 매칭펀드 및 인센티브를 확대하겠습니다.",
            2: "기후테크컨페어 사전 홍보를 위한 네트워킹행사를 열고 빅데이터 기반 스마트인프라 구축을 위해 청소년 대상 테스트베드 및 라운드테이블을 진행합니다.",
            3: "이번 디깅위크 기간 동안 시민들의 니즈를 반영하여 심포지엄을 열고 해외 모범 사례를 벤치마킹하여 원스톱 서비스를 구축하겠습니다."
        };

        // Real contextual sentence examples
        const realWorldExamples = [
            {
                tag: "최신 정책",
                before: "우리 시는 <span class='text-red-600 font-semibold'>ESG</span> 가치를 실현하고 <span class='text-red-600 font-semibold'>바이오헬스 첨단클러스터</span> 조성을 위한 <span class='text-red-600 font-semibold'>이노베이션포럼</span>을 추진합니다.",
                after: "우리 시는 <span class='text-emerald-700 font-semibold'>친환경·사회·투명 경영</span> 가치를 실현하고 <span class='text-emerald-700 font-semibold'>생명 건강 첨단 단지</span> 조성을 위한 <span class='text-emerald-700 font-semibold'>혁신 토론회</span>를 추진합니다."
            },
            {
                tag: "행사 및 학술",
                before: "미래 <span class='text-red-600 font-semibold'>모빌리티</span> 관련 <span class='text-red-600 font-semibold'>기후테크컨페어</span>와 함께 참가자 간 <span class='text-red-600 font-semibold'>네트워킹행사</span>가 개최될 예정입니다.",
                after: "미래 <span class='text-emerald-700 font-semibold'>이동 수단</span> 관련 <span class='text-emerald-700 font-semibold'>기후 기술 박람회</span>와 함께 참가자 간 <span class='text-emerald-700 font-semibold'>교류 행사</span>가 개최될 예정입니다."
            },
            {
                tag: "교육 및 행정",
                before: "청소년의 <span class='text-red-600 font-semibold'>미디어 리터러시</span> 증진을 위해 이번 <span class='text-red-600 font-semibold'>디깅위크</span> 기간 중 <span class='text-red-600 font-semibold'>하이브리드</span> 형태의 <span class='text-red-600 font-semibold'>심포지엄</span>을 운영합니다.",
                after: "청소년의 <span class='text-emerald-700 font-semibold'>매체 이해력</span> 증진을 위해 이번 <span class='text-emerald-700 font-semibold'>취향 탐구 주간</span> 기간 중 <span class='text-emerald-700 font-semibold'>혼합형</span> 형태의 <span class='text-emerald-700 font-semibold'>학술 토론회</span>를 운영합니다."
            }
        ];

        // Init App on Load
        window.addEventListener('DOMContentLoaded', () => {
            renderQuickTags();
            renderDictionary();
            renderExamples();
            lucide.createIcons();
        });

        // Tab Switcher
        function switchTab(tabName) {
            const tabs = ['converter', 'dictionary', 'examples'];
            tabs.forEach(t => {
                const sec = document.getElementById(`sec-${t}`);
                const btn = document.getElementById(`tab-${t}`);
                if (t === tabName) {
                    sec.classList.remove('hidden');
                    btn.className = "flex-1 min-w-[120px] py-2.5 rounded-lg flex items-center justify-center gap-2 transition-all bg-gov-600 text-white shadow-sm font-semibold";
                } else {
                    sec.classList.add('hidden');
                    btn.className = "flex-1 min-w-[120px] py-2.5 rounded-lg flex items-center justify-center gap-2 transition-all text-slate-600 hover:text-slate-900 hover:bg-slate-100";
                }
            });
        }

        // ==========================================
        // TAB 1: CONVERTER LOGIC
        // ==========================================
        function setSample(num) {
            document.getElementById('inputText').value = sampleSentences[num] || '';
            convertText();
        }

        function clearInput() {
            document.getElementById('inputText').value = '';
            document.getElementById('outputText').innerHTML = '<span class="text-slate-400 italic">왼쪽 창에 글을 입력한 후 [순화하기] 버튼을 누르시면, 변환된 결과와 변경 단어가 표시됩니다.</span>';
            document.getElementById('convertedWordsSummary').innerHTML = '<span class="font-medium text-slate-500">순화 내역:</span> <span class="text-slate-400 italic">변환된 단어가 없습니다.</span>';
        }

        function convertText() {
            const text = document.getElementById('inputText').value;
            if (!text.trim()) {
                showToast("변환할 문장을 먼저 입력해주세요.");
                return;
            }

            let resultText = text;
            const replacedMap = new Map();

            // Sort by word length descending to prevent overlapping substring replacement
            const sortedDict = [...dictionaryData].sort((a, b) => b.original.length - a.original.length);

            sortedDict.forEach(item => {
                const regex = new RegExp(escapeRegExp(item.original), 'gi');
                if (regex.test(resultText)) {
                    const cleanTarget = item.target.split('/')[0].trim();
                    replacedMap.set(item.original, cleanTarget);
                    resultText = resultText.replace(regex, `<span class="highlight-word" title="${item.original} → ${cleanTarget}">${cleanTarget}</span>`);
                }
            });

            const outputDiv = document.getElementById('outputText');
            outputDiv.innerHTML = resultText;
const summaryDiv = document.getElementById('convertedWordsSummary');
            if (replacedMap.size > 0) {
                let tagsHtml = '';
                replacedMap.forEach((target, orig) => {
                    tagsHtml += `<span class="inline-flex items-center gap-1 bg-amber-50 text-amber-800 border border-amber-200 px-2 py-0.5 rounded-md text-xs mr-1.5 mb-1 font-medium">
                        <span class="line-through text-slate-400">${orig}</span> → <strong>${target}</strong>
                    </span>`;
                });
                summaryDiv.innerHTML = `<div class="font-medium text-slate-700 mb-1">총 ${replacedMap.size}개 용어 순화 완료:</div><div>${tagsHtml}</div>`;
            } else {
                summaryDiv.innerHTML = '<span class="font-medium text-slate-500">순화 내역:</span> <span class="text-slate-500">등록된 어려운 공공언어가 발견되지 않았습니다.</span>';
            }
        }

        function escapeRegExp(string) {
            return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
        }

        function copyOutput() {
            const outputDiv = document.getElementById('outputText');
            const rawText = outputDiv.innerText || outputDiv.textContent;
            if (!rawText || rawText.includes("왼쪽 창에 글을 입력한 후")) {
                showToast("복사할 변환 결과가 없습니다.");
                return;
            }

            const tempTextArea = document.createElement('textarea');
            tempTextArea.value = rawText;
            document.body.appendChild(tempTextArea);
            tempTextArea.select();
            document.execCommand('copy');
            document.body.removeChild(tempTextArea);

            showToast("순화된 문장이 클립보드에 복사되었습니다!");
        }

        // Custom User Word Functionality
        function addUserWord() {
            const orig = document.getElementById('newOrig').value.trim();
            const target = document.getElementById('newTarget').value.trim();
            const category = document.getElementById('newCategory').value;
            const desc = document.getElementById('newDesc').value.trim();

            if (!orig || !target) {
                showToast("어려운 단어와 순화어는 필수입니다.");
                return;
            }

            const newObj = {
                id: Date.now(),
                original: orig,
                target: target,
                category: "사용자 추가",
                desc: desc || "사용자가 직접 추가한 순화어입니다.",
                example: `${orig} → ${target}`
            };

            dictionaryData.unshift(newObj);
            closeModal('addWordModal');
            document.getElementById('newOrig').value = '';
            document.getElementById('newTarget').value = '';
            document.getElementById('newDesc').value = '';

            renderQuickTags();
            renderDictionary();
            showToast(`'${orig}' 용어가 성공적으로 추가되었습니다!`);
        }

        function renderQuickTags() {
            const container = document.getElementById('quickTags');
            const popular = dictionaryData.filter(d => [201, 202, 203, 204, 205, 206, 207, 208, 101, 106, 107, 111, 1, 3, 11].includes(d.id));
            container.innerHTML = popular.map(item => `
                <button onclick="insertAndConvert('${item.original}')" class="bg-slate-100 hover:bg-gov-50 hover:text-gov-700 hover:border-gov-300 border border-slate-200 text-slate-700 px-2.5 py-1.5 rounded-lg transition flex items-center gap-1.5 shrink-0">
                    <span class="font-semibold">${item.original}</span>
                    <span class="text-slate-400 text-[10px]">→ ${item.target.split('/')[0]}</span>
                </button>
            `).join('');
        }

        function insertAndConvert(word) {
            const input = document.getElementById('inputText');
            if (input.value) {
                input.value += ` ${word}`;
            } else {
                input.value = `이 안건에 대한 ${word} 관련 대책을 추진합니다.`;
            }
            convertText();
        }

        // ==========================================
        // TAB 2: DICTIONARY LOGIC
        // ==========================================
        function setCategory(cat) {
            activeCategory = cat;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.className = "cat-btn px-3 py-1.5 rounded-full bg-slate-100 text-slate-600 hover:bg-slate-200";
            });
            const activeBtn = document.getElementById(`cat-${cat}`);
            if (activeBtn) {
                activeBtn.className = "cat-btn px-3 py-1.5 rounded-full bg-gov-600 text-white font-semibold";
            }
            renderDictionary();
        }

        function clearDictSearch() {
            document.getElementById('dictSearchInput').value = '';
            document.getElementById('dictClearBtn').classList.add('hidden');
            renderDictionary();
        }

        function renderDictionary() {
            const query = document.getElementById('dictSearchInput').value.trim().toLowerCase();
            const clearBtn = document.getElementById('dictClearBtn');
            
            if (query) {
                clearBtn.classList.remove('hidden');
            } else {
                clearBtn.classList.add('hidden');
            }

            const grid = document.getElementById('dictionaryGrid');
            
            const filtered = dictionaryData.filter(item => {
                const matchesCat = (activeCategory === 'all' || item.category === activeCategory);
                const matchesQuery = !query || 
                    item.original.toLowerCase().includes(query) || 
                    item.target.toLowerCase().includes(query) ||
                    item.desc.toLowerCase().includes(query);
                return matchesCat && matchesQuery;
            });

            document.getElementById('dictResultCount').innerText = filtered.length;

            if (filtered.length === 0) {
                grid.innerHTML = `
                    <div class="col-span-full py-12 text-center text-slate-400 space-y-2">
                        <i data-lucide="search-x" class="w-10 h-10 mx-auto text-slate-300"></i>
                        <p class="text-sm">검색 결과가 없습니다.</p>
                        <p class="text-xs text-slate-400">다른 검색어로 다시 시도해보세요.</p>
                    </div>
                `;
                lucide.createIcons();
                return;
            }

            grid.innerHTML = filtered.map(item => `
                <div class="bg-white rounded-xl p-4 border border-slate-200 hover:border-gov-300 shadow-sm hover:shadow transition flex flex-col justify-between space-y-3">
                    <div>
                        <div class="flex justify-between items-start mb-2">
                            <span class="text-xs px-2 py-0.5 rounded font-medium bg-slate-100 text-slate-600">${item.category}</span>
                            <button onclick="copyWord('${item.original}', '${item.target}')" title="단어 복사" class="text-slate-400 hover:text-gov-600 transition">
                                <i data-lucide="copy" class="w-3.5 h-3.5"></i>
                            </button>
                        </div>
                        <div class="flex items-center gap-2 mb-1">
                            <h4 class="text-base font-bold text-red-600">${item.original}</h4>
                            <i data-lucide="arrow-right" class="w-4 h-4 text-slate-400"></i>
                            <h4 class="text-base font-bold text-emerald-700">${item.target}</h4>
                        </div>
                        <p class="text-xs text-slate-600 leading-relaxed">${item.desc}</p>
                    </div>
                    <div class="bg-slate-50 p-2.5 rounded-lg border border-slate-100 text-xs text-slate-700">
                        <span class="font-semibold text-slate-500 block mb-0.5">사용 예시:</span>
                        <p class="italic">${item.example}</p>
                    </div>
                </div>
            `).join('');

            lucide.createIcons();
        }

        function copyWord(orig, target) {
            const text = `${orig} -> ${target}`;
            const tempTextArea = document.createElement('textarea');
            tempTextArea.value = text;
            document.body.appendChild(tempTextArea);
            tempTextArea.select();
            document.execCommand('copy');
            document.body.removeChild(tempTextArea);

            showToast(`'${orig} → ${target}'가 복사되었습니다.`);
        }

        // ==========================================
        // TAB 3: EXAMPLES LOGIC
        // ==========================================
        function renderExamples() {
            const container = document.getElementById('exampleSentenceList');
            container.innerHTML = realWorldExamples.map(item => `
                <div class="border border-slate-200 rounded-xl p-4 bg-slate-50 space-y-3">
                    <span class="inline-block bg-gov-700 text-white text-xs font-semibold px-2.5 py-0.5 rounded-md">${item.tag} 사례</span>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-sm">
                        <div class="bg-white p-3.5 rounded-lg border border-red-200 shadow-sm">
                            <span class="text-xs font-bold text-red-600 flex items-center gap-1 mb-1">
                                <i data-lucide="x-circle" class="w-3.5 h-3.5"></i> 기존 공문서 (어려운 표현)
                            </span>
                            <p class="text-slate-800 leading-relaxed">${item.before}</p>
                        </div>
                        <div class="bg-white p-3.5 rounded-lg border border-emerald-200 shadow-sm">
                            <span class="text-xs font-bold text-emerald-600 flex items-center gap-1 mb-1">
                                <i data-lucide="check-circle" class="w-3.5 h-3.5"></i> 순화 후 (쉬운 우리말)
                            </span>
                            <p class="text-slate-800 leading-relaxed">${item.after}</p>
                        </div>
                    </div>
                </div>
            `).join('');
            lucide.createIcons();
        }

        // ==========================================
        // MODAL & TOAST UTILS
        // ==========================================
        function openModal(id) {
            document.getElementById(id).classList.remove('hidden');
        }

        function closeModal(id) {
            document.getElementById(id).classList.add('hidden');
        }

        function showToast(msg) {
            const toast = document.getElementById('toast');
            document.getElementById('toastMsg').innerText = msg;
            toast.classList.remove('translate-y-20', 'opacity-0');
            toast.classList.add('translate-y-0', 'opacity-100');

            setTimeout(() => {
                toast.classList.remove('translate-y-0', 'opacity-100');
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 2500);
        }
    </script>
</body>
</html>