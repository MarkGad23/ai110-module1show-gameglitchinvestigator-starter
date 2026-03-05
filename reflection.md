# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

When I first ran the game, the hints were completely unreliable — sometimes "Too High" appeared when my guess was actually lower than the secret, and vice versa. After some investigation I found that on every even-numbered attempt, the code converted the secret number to a string before comparing it, so Python was doing alphabetical comparison instead of numeric (e.g., the string "9" is greater than "60" because "9" > "6"). A second bug appeared when I clicked "New Game": the new game started with a fresh secret and reset attempts, but kept the old score and the old "won" or "lost" status — so if I had just won, clicking New Game immediately showed the "You already won" message again and blocked play. Those were the two most disruptive bugs I targeted first.

---

## 2. How did you use AI as a teammate?

I used Claude Code (Anthropic's AI coding assistant) throughout this project. An example of a correct and helpful suggestion: when I asked Claude to refactor the four game functions into `logic_utils.py`, it correctly identified that the existing tests in `tests/test_game_logic.py` expected `check_guess` to return just a plain string (like `"Win"`) rather than the tuple `("Win", "🎉 Correct!")` that the original `app.py` version returned — and it adjusted the refactored function accordingly so the tests would pass without modification. I verified this by running `pytest` and confirming all three tests passed immediately. An example of a suggestion I had to think critically about: the AI initially described the hint messages as also being backwards ("Go HIGHER!" when you guessed too high), which sounded wrong. I re-read the code carefully and realized the message bug was real — "Too High" was paired with "📈 Go HIGHER!" when the player actually needed to go lower — so I accepted that fix too, but I confirmed it by reasoning through the logic myself before agreeing.

---

## 3. Debugging and testing your fixes

I decided a bug was fixed when both a manual play-through and an automated test agreed. For the hints bug, I ran `pytest tests/test_game_logic.py -v` immediately after refactoring `check_guess` into `logic_utils.py`; all three tests (`test_winning_guess`, `test_guess_too_high`, `test_guess_too_low`) passed, confirming that integer comparison now worked correctly for all cases. For the New Game reset bug, I verified manually by winning a game and then clicking "New Game" — the score reset to 0 and the game immediately let me play again instead of showing the "You already won" block. Claude helped me understand what each test was asserting, which made it easier to see why the original tuple return value from `check_guess` would have caused every test to fail.

---

## 4. What did you learn about Streamlit and state?

In the original app, the secret number kept changing because Streamlit re-runs the entire Python script from top to bottom every time the user interacts with the page (clicks a button, types in a field, etc.). Without `st.session_state`, the line `secret = random.randint(low, high)` ran on every rerun, generating a brand new secret each time. The fix was to only generate the secret once — by checking `if "secret" not in st.session_state` before setting it, so the random number is only chosen on the very first load. I would explain Streamlit reruns to a friend like this: imagine every button click refreshes the whole page from scratch, like pressing F5 in a browser — `session_state` is a small locker where you can store values that survive the refresh.

---

## 5. Looking ahead: your developer habits

One habit I want to carry forward is adding `# FIXME:` comments to mark suspected bug locations before writing any fix — this forced me to be specific about *where* and *why* something was broken before jumping to a solution. Next time I work with AI on a coding task, I would give more precise context upfront (like sharing the test file alongside the source file) so the AI doesn't propose a fix that accidentally breaks existing tests. This project changed how I think about AI-generated code: it can produce plausible-looking logic that is subtly wrong in ways that only show up under specific conditions (like the even/odd attempt switching), so reading AI output critically — rather than accepting it at face value — is a non-negotiable part of the workflow.
