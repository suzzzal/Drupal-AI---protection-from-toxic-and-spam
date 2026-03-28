# Technical Documentation: Real-Time AI Content Moderation Layer

## 1. Project Overview
This project aims to implement a client-side integration layer that provides real-time toxicity feedback to users as they type. By utilizing a **Debounce Engine** and a **Provider Factory**, the system minimizes unnecessary API overhead while maintaining a flexible architecture that supports multiple AI moderation services (e.g., OpenAI, Gemini, Perspective).

---

## 2. Theoretical Architecture & Logic

### 2.1 The Debounce Strategy
Directly binding API calls to every keystroke (the `onInput` event) creates two significant problems: 
1. **Network Congestion:** Excessive HTTP requests can lead to rate-limiting and high latency.
2. **Cost Inefficiency:** AI providers charge per request or token; calling them for every character is economically unfeasible.

The **700ms Cancelable Debounce** acts as a temporal buffer. When a user types, a timer starts. If another key is pressed before 700ms elapses, the previous timer is canceled and a new one starts. The API is only triggered once the user pauses for the full duration, ensuring we only analyze "settled" intent.

### 2.2 Provider Factory Pattern
To avoid "Vendor Lock-in," the system employs a Factory Pattern. This creates an abstraction layer between the UI and the AI service. 
* **Standardized Interface:** Regardless of whether the backend is OpenAI or Gemini, the client-side UI receives a uniform JSON object (e.g., `{ isToxic: boolean, categories: [] }`).
* **Scalability:** Adding a new provider only requires a new class implementation rather than a rewrite of the UI logic.


### 2.3 Feedback Loop & UX Theory
The system utilizes a "Pre-submission Intervention" model. By providing an inline warning banner and red underlines (squiggles), we nudge user behavior toward community compliance before a violation is actually recorded in the database.

---

## 3. Execution Strategy

### Phase 1: Core Engine (Days 1-3)
* Develop the `toxic_content_guard.js` module.
* Implement the core debounce function using `setTimeout` and `clearTimeout`.
* Establish the `BaseProvider` abstract class to define the contract for all future AI integrations.

### Phase 2: Integration & Testing (Days 4-10)
* Implement `OpenAIProvider` as the primary driver.
* Develop the `ProviderFactory` to handle logic for switching between services.
* Write unit tests specifically for the debounce trigger to ensure `n` keystrokes result in `1` API call.

### Phase 3: UI/UX Implementation (Days 11-14)
* Create the CSS for the "Warning Banner" and "Inline Popup."
* Integrate the AI response with the DOM to toggle visibility of warnings based on the `isToxic` flag.

---

## 4. Flowchart Logic


1. **User Input** -> 2. **Clear Existing Timer** -> 3. **Start 700ms Timer** -> 4. **Timer Completes?** - No (New Input): Return to step 2.
   - Yes: Proceed to step 5.
5. **Request to ProviderFactory** -> 6. **AI Analysis** -> 7. **Return Boolean/Metadata** -> 8. **Update UI Elements**.

---

## 5. Technical Implementation (Reference Code)

### 5.1 The Guard Module (`toxic_content_guard.js`)
```javascript
/**
 * Core module for handling input debouncing and AI service routing.
 */

class ToxicContentGuard {
    constructor(provider) {
        this.provider = provider;
        this.timeout = null;
    }

    /**
     * Debounce function to limit API hits.
     * @param {string} text - The user input.
     * @param {Function} callback - UI update function.
     */
    analyze(text, callback) {
        if (this.timeout) {
            clearTimeout(this.timeout);
        }

        this.timeout = setTimeout(async () => {
            if (text.trim().length < 5) return; // Ignore very short inputs
            
            try {
                const result = await this.provider.checkToxicity(text);
                callback(result);
            } catch (error) {
                console.error("Moderation Error:", error);
            }
        }, 700);
    }
}
```

## 5.2 Provider Factory
```JavaScript
class ProviderFactory {
    static getProvider(type, apiKey) {
        switch (type) {
            case 'OPENAI':
                return new OpenAIProvider(apiKey);
            case 'GEMINI':
                return new GeminiProvider(apiKey);
            default:
                throw new Error("Provider not supported");
        }
    }
}

class OpenAIProvider {
    constructor(apiKey) {
        this.apiKey = apiKey;
    }

    async checkToxicity(text) {
        // Conceptual implementation for OpenAI Moderation Endpoint
        const response = await fetch('[https://api.openai.com/v1/moderations](https://api.openai.com/v1/moderations)', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'Authorization': `Bearer ${this.apiKey}`
            },
            body: JSON.stringify({ input: text })
        });
        const data = await response.json();
        return {
            flagged: data.results[0].flagged,
            categories: data.results[0].categories
        };
    }
}

