1. Character Sets: The "Control Character" Filter

In qual_clean.py, we need to strip non-printable "noise" that can break database storage.

    Regex: [\x00-\x1F]

    The Feature: Character Sets [] match any single character within the brackets.

    The Trick: Using hex codes (\xHH) allows you to target characters you can't type, like "Null" or "Escape".

    Try it on this text: Data\x01Integrity\x1B_Test (Notice how it highlights the hidden "Start of Header" and "Escape" characters).

2. Anchors & Quantifiers: The "Markdown Header" Stripper

To clean qualitative text, we remove formatting like # or ## at the start of lines.

    Regex: ^#+\s

    The Features: * Anchor ^: Forces the match to start at the beginning of the string.

    Quantifier +: Matches 1 or more of the preceding character (in this case, #).

Try it on this text:
Markdown

# Research Goal
## Methodology
### Results

Pro Tip: In your Python code, you’ll need the re.MULTILINE flag so ^ catches the start of every line.

3. Capturing Groups: The "Commit Type" Extractor

The first step of parsing a Conventional Commit is identifying the "type" (e.g., feat, fix).

    Regex: ^(\w+): 

The Feature: Capturing Groups () allow you to "isolate" a part of the match to pull out later.

The Shorthand: \w matches any "word" character (letters, numbers, and underscores).

    Try it on this text: feat: add user authentication

    Check: Your tool should show "Group 1" as feat.

4. Greedy vs. Lazy: The "Inline Code" Capture

When finding code inside backticks (`...`), we have to be careful not to "over-match".

    Regex (Greedy): `.*` 

Regex (Lazy): `.*?`

The Difference: Greedy takes as much as possible; Lazy takes the bare minimum.

    Try it on this text: The function `init()` calls `start()` immediately.

    Observation: The Greedy version will match from the first backtick to the very last one (including the word "calls"), while Lazy correctly finds two separate matches.

5. Non-Capturing Groups: The "Optional Scope"

Some commits have a scope—feat(auth):—and some don't—feat:. We need to group the scope without "cluttering" our result list.

    Regex: ^(?:\((\w+)\))? 

The Feature: Non-Capturing Group (?:...) groups characters for logic (like making them optional) but doesn't save them as a separate "match".

The Optional Quantifier ?: Makes the entire group occur 0 or 1 times.

Try it on this text:

    (ui)

    (Empty string)

Why it's in qual_clean: We use this to wrap the parentheses so we can say "the parentheses AND the text inside are optional".