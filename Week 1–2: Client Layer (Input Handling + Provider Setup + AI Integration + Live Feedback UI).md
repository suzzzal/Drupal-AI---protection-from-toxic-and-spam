# Week 1–2: Client Layer (Input Handling + Provider Setup + AI Integration + Live Feedback UI)

## Execution Strategy

- Build step-by-step: debounce first → plug in one AI provider → add ProviderFactory → scale to multiple providers → layer on real-time UI.  
- Keep things clean: separate input handling, provider logic, response normalization, and UI so nothing gets messy or tightly coupled.  
- Optimize hard: use cancelable debounce, avoid duplicate calls, and only hit APIs when the user actually pauses typing.  
- Normalize everything: force all provider responses into one clean schema so UI and future backend logic stay consistent.  
- Make it solid: test edge cases, handle API failures gracefully, and ensure accessibility with ARIA-based feedback.



## Completion Criteria

- Debounce works properly and fires only once after input stabilizes (no spam API calls).  
- ProviderFactory supports at least one provider and can easily plug in more.  
- AI responses are normalized and reflected correctly in real-time UI feedback.  
- Edge cases (rapid typing, empty input, API failure) don’t break the flow.  
- Tests pass cleanly, UI feels smooth, and no unnecessary API calls are happening.



   
