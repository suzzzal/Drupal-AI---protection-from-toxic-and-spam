# Week 1–2: Client Layer (Input Handling + Provider Setup + AI Integration + Live Feedback UI)

## Execution Strategy

- Build step-by-step: debounce first → plug in one AI provider → add ProviderFactory → scale to multiple providers → layer on real-time UI.  
- Keep things clean: separate input handling, provider logic, response normalization, and UI so nothing gets messy or tightly coupled.  
- Optimize hard: use cancelable debounce, avoid duplicate calls, and only hit APIs when the user actually pauses typing.  
- Normalize everything: force all provider responses into one clean schema so UI and future backend logic stay consistent.  
- Make it solid: test edge cases, handle API failures gracefully, and ensure accessibility with ARIA-based feedback.

---

## Completion Criteria

- Debounce works properly and fires only once after input stabilizes (no spam API calls).  
- ProviderFactory supports at least one provider and can easily plug in more.  
- AI responses are normalized and reflected correctly in real-time UI feedback.  
- Edge cases (rapid typing, empty input, API failure) don’t break the flow.  
- Tests pass cleanly, UI feels smooth, and no unnecessary API calls are happening.


# WEEK 3-4 Token Generation + Client Validation + Form Integration & Configuration Layer (June 8 – June 21) 

## Execution Strategy

- Lock in the client → server trust flow by generating tokens on the client and validating securely on the server (no bypass vibes).  
- Integrate validation across all Drupal forms (comment, node, webform, contact) in parallel, keeping each flow isolated for easier debugging.  
- Keep token logic, provider logic, and form integration decoupled so the system stays modular and clean.  
- Build admin config early so thresholds, providers, and toggles are not hardcoded and can be tuned easily.  
- Test every path (valid, invalid, missing token) + fallback scenarios to make sure nothing slips through.

---

## Completion Criteria

- Token generation + HMAC validation working end-to-end with replay protection (timestamp + nonce).  
- All form types correctly integrated with validation logic and no bypass possible.  
- Admin config page fully functional (provider selection, API keys, thresholds, toggles).  
- Detoxify (FastAPI) provider working alongside existing provider setup.  
- Tests pass for all validation paths (valid/invalid/missing + fallback) with no regressions.


# WEEK 5-6 Gatekeeper (Token Validation) + Backend Test Suite + Midterm Polish(June 22 – July 5)

## Execution Strategy

- Build a solid gatekeeper layer in `hook_form_validate()` to verify tokens server-side (HMAC + timestamp) so nothing shady gets through.  
- Handle all validation paths cleanly (valid, invalid, missing) with proper routing and fallback to server-side AI when JS is bypassed.  
- Keep validation logic modular (signature check, timestamp check, fallback) so each part is testable and not tightly coupled.  
- Add role-based bypass (admin/editor) via config without breaking core validation flow.  
- Go heavy on testing: unit tests for logic, kernel tests for integration, and cover all edge cases before moving forward.

---

## Completion Criteria

- Token validation correctly handles valid, invalid, and missing cases with replay protection (timestamp window).  
- Secure fallback to server-side AI works when client-side validation is bypassed.  
- Role-based bypass rules function correctly without compromising security.  
- Full test coverage for validation logic (unit + kernel) with all edge cases handled.  
- Backend is stable with no regressions in validation or submission flow.


# WEEK 7-8 :  Keyword Filtering + FastAPI Microservice + Server-Side AI  (July 13 – July 26) 


## Execution Strategy

- Build a fast keyword filtering layer first as a “cheap check” to catch obvious violations before hitting AI (save cost + latency).  
- Keep keyword logic separate from admin UI and decision engine so it stays modular and easy to extend.  
- Develop FastAPI microservice and Drupal integration in parallel to avoid integration bottlenecks later.  
- Standardize API response format (`score, categories, spans`) across all providers to keep downstream logic clean.  
- Implement strong fallback handling (API fail → keyword-only moderation) and ensure logging for observability.

---

## Completion Criteria

- KeywordCheckerService accurately detects patterns (case-insensitive, regex, Unicode) and routes directly without invoking AI.  
- FastAPI microservice is fully functional, containerized (Docker), and integrated with Drupal.  
- Multiple AI providers (Detoxify, HuggingFace, etc.) are working with consistent response structure.  
- Fallback mechanism works reliably when AI services fail, without breaking moderation flow.  
- API usage is reduced for obvious violations, and all edge cases are covered through tests.


# WEEK 9-10 :  Decision Engine + Moderation Queue + Full Content Type Integration + Start working on Dashboard (July 27 – Aug 9) 


## Execution Strategy

- Define routing logic upfront in the DecisionEngine (keyword + AI + thresholds) so behavior is predictable and auditable.  
- Keep decision logic separate from storage (reports) and workflow (moderation queue) to avoid tight coupling.  
- Integrate each content type (comments, nodes, webforms, contact) independently to isolate bugs and ensure stability.  
- Use consistent test fixtures (predefined toxic inputs) across all forms to validate routing accuracy.  
- Validate full end-to-end flow (input → decision → routing → queue/dashboard) before expanding dashboard features.

---

## Completion Criteria

- DecisionEngine correctly routes content into publish, moderation queue, or reject states based on combined signals.  
- Moderation queue is populated with flagged content and includes detailed toxicity reports.  
- All supported content types are fully integrated with consistent moderation behavior.  
- Test mode works correctly for safe threshold tuning (all content routed to queue).  
- Initial dashboard displays flagged content, scores, and decisions accurately with no routing regressions.

# WEEK 11-12 :  Finish Dashboard+ Caching + Final Tests + Documentation (Aug 10 – Aug 24) 

## Execution Strategy

- Finish dashboard UI and backend in parallel, ensuring smooth data flow (reports → UI → actions) without performance bottlenecks.  
- Optimize API usage using cache-first strategy (content-hash based caching) before scaling testing or handling load.  
- Ensure moderator workflows are clean and efficient (quick actions, filters, clear insights) for real usability.  
- Stabilize the system by prioritizing functional + accessibility tests before packaging or release.  
- Convert all implementation notes into structured documentation and enforce accessibility (WCAG + ARIA) across UI.

---

## Completion Criteria

- Moderation dashboard fully functional with filtering, sorting, detailed views, and inline actions.  
- API usage reduced through caching, with stable performance under repeated or high-volume submissions.  
- Functional, admin, and accessibility tests pass consistently in CI with zero regressions.  
- Documentation (README, deployment, developer guide) is complete, clear, and usable.  
- Module is packaged, release-ready (Drupal.org), and demo video showcases full end-to-end workflow.
