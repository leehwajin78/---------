# Step 3. 테스트 태스크 (AC → 테스트 코드 변환)

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| TEST-001 | F1 브리프 | 20문항 미만 입력 시 400 에러 반환 + Gemini 미호출 검증 테스트 | REQ-FUNC-003 AC | CMD-005 | M |
| TEST-002 | F1 브리프 | 브리프 생성 완료 시 가치선언문/핵심타깃/강의주제 3종 구조화 출력 검증 테스트 | REQ-FUNC-004 AC | CMD-006 | M |
| TEST-003 | F1 브리프 | 검수 수정 시 version 자동 증가 + 이전 버전 보존 검증 테스트 | REQ-FUNC-006 AC | CMD-007 | M |
| TEST-004 | F1 브리프 | Streaming 출력 시 빈 화면 대기 시간 0초 검증 테스트 | REQ-FUNC-002 AC | CMD-006 | H |
| TEST-005 | F2 진단 | 5문항 정상 응답 → 10초 이내 리포트 생성 검증 테스트 | REQ-FUNC-008 AC | CMD-001 | M |
| TEST-006 | F2 진단 | 전 문항 동일 선택 시 422 에러 + 불성실 메시지 출력 + Gemini 미호출 검증 테스트 | REQ-FUNC-009 AC | CMD-002 | M |
| TEST-007 | F2 진단 | 불성실 필터 정확도 ≥95%, 정상 응답 오차단율 <2% 검증 테스트 | REQ-FUNC-009 AC | CMD-002 | H |
| TEST-008 | F2 진단 | CTA 버튼 노출 + 클릭 시 상담 신청 폼 이동 검증 테스트 | REQ-FUNC-010 AC | QRY-001 | L |
| TEST-009 | F2 진단 | cta_click GA4 이벤트 전송 + 실패율 <1% 검증 테스트 | REQ-FUNC-011 AC | UI-005 | M |
| TEST-010 | F4 리드 | 상담 신청 → DB 저장 성공률 ≥99.5% 검증 테스트 | REQ-FUNC-019 AC | CMD-003 | M |
| TEST-011 | F4 리드 | 이메일 형식 오류/연락처 누락 시 200ms 이내 인라인 에러 + DB 저장 0건 검증 테스트 | REQ-FUNC-020 AC | CMD-003 | M |
| TEST-012 | F4 리드 | 리드 상태 변경 + 변경일시 자동 기록 검증 테스트 | REQ-FUNC-021 AC | CMD-004 | L |
| TEST-013 | F4 리드 | 리드 적재 실패 시 1분 이내 Slack 알림 발송 검증 테스트 | REQ-FUNC-022 AC | CMD-013 | M |
| TEST-014 | F3 관리자 | 녹취 텍스트 500자 이하 시 차단 경고 + Gemini 미호출 검증 테스트 | REQ-FUNC-015 AC | CMD-009 | M |
| TEST-015 | F3 관리자 | 미등록 IP 접속 차단율 100% + 차단 로그 기록 + Slack 알림 검증 테스트 | REQ-FUNC-016 AC | SEC-001 | H |
| TEST-016 | 공통 | Gemini API 5xx/타임아웃 시 Fallback 메시지 출력 + Slack 알림 검증 테스트 | REQ-FUNC-030 AC | CMD-014 | M |
| TEST-017 | F6 리테이너 | 구독 갱신/일시정지/해지 이력 추적 검증 테스트 | REQ-FUNC-026 AC | CMD-011 | M |
| TEST-018 | 공통 | 법인 정보 저장 + 세금계산서 조회 검증 테스트 | REQ-FUNC-029 AC | CMD-012 | L |
