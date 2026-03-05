# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable.

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip3 install -r requirements.txt`
2. Run the app: `python3 -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

**Purpose:** A number guessing game where the player tries to guess a secret number within a set number of attempts. The difficulty setting changes the number range and attempt limit.

**Bugs found:**
1. **Misleading hints** — On every even-numbered attempt, the secret number was converted to a string before comparison. This caused Python to do string comparison (`"60" < "9"` is `True` because `"6" < "9"` alphabetically), giving completely wrong "Too High" / "Too Low" feedback.
2. **New Game didn't reset** — Clicking "New Game" picked a new secret and reset attempts, but left `score` and `status` unchanged. If you had won or lost, the game immediately showed the end screen again on the next game.

**Fixes applied:**
1. Moved all game logic (`get_range_for_difficulty`, `parse_guess`, `check_guess`, `update_score`) into `logic_utils.py`. The fixed `check_guess` always compares integers directly — no string conversion.
2. Added `st.session_state.score = 0`, `st.session_state.status = "playing"`, and `st.session_state.history = []` to the New Game block so every game starts fully fresh.

## 📸 Demo

- [ ] [Insert a screenshot of your fixed, winning game here]

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
