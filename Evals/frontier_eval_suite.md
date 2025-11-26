# Extreme Frontier Model Eval Suite

A curated collection of Hard and Very Hard evaluation tasks designed to probe the bleeding edge of model capabilities. These evals specifically target:

- **Self-referential constraint satisfaction** (the answer changes the problem)
- **Multi-constraint generation** (can't verify until committed)
- **True spatial/mechanical simulation** (not pattern-matching)
- **Adversarial backtracking requirements** (obvious paths fail)
- **Real-time accounting of generated content** (counting your own output)

---

## Table of Contents

### Tier 1: Extreme (New)
1. [Fixed-Point Paragraph](#fixed-point-paragraph)
2. [Mutually Describing Sentences](#mutually-describing-sentences)
3. [Verbal Rubik's Cube](#verbal-rubiks-cube)
4. [Steganographic Review](#steganographic-review)
5. [Counting Constraint Cascade](#counting-constraint-cascade)
6. [Pathological Logic Grid](#pathological-logic-grid)
7. [Precise Bitmap](#precise-bitmap)
8. [Interleaved Dual Narrative](#interleaved-dual-narrative)
9. [Unbounded Calendar Chain](#unbounded-calendar-chain)
10. [Self-Counting Story](#self-counting-story)

### Tier 2: Very Hard (Previous)
11. [Self-Counting Sentence (Autogram)](#self-counting-sentence-autogram)
12. [Reverse Quine](#reverse-quine)
13. [Self-Referential Crossword](#self-referential-crossword)
14. [Consistent Character Turnaround](#consistent-character-turnaround)
15. [Hands Holding Specific Playing Cards](#hands-holding-specific-playing-cards)

### Tier 3: Hard (Previous)
16. [Pangram Dialogue](#pangram-dialogue)
17. [Knot Description and Visualization](#knot-description-and-visualization)
18. [Constraint Satisfaction with Backtracking](#constraint-satisfaction-with-backtracking)
19. [Geometric Construction Reasoning](#geometric-construction-reasoning)
20. [Pronoun Resolution Across Narrative Complexity](#pronoun-resolution-across-narrative-complexity)
21. [Regex Golf](#regex-golf)
22. [Accurate Venn Diagram](#accurate-venn-diagram)
23. [Self-Documenting Function](#self-documenting-function)

---

# Tier 1: Extreme

---

<eval id="EX-001">
<name>Fixed-Point Paragraph</name>
<domain>Constrained Generation / Planning</domain>
<difficulty>Extreme</difficulty>

<prompt>
Write a coherent, grammatically correct paragraph where:
- Word 1 has exactly 1 letter
- Word 2 has exactly 2 letters  
- Word 3 has exactly 3 letters
- Word 4 has exactly 4 letters
- And so on...

Continue this pattern for as many words as possible while maintaining:
1. Grammatical correctness (it must be a valid English paragraph)
2. Semantic coherence (it must make sense, not be word salad)
3. Natural flow (it should read reasonably smoothly)

Do not use proper nouns, hyphenated words, or contractions. Each word must be a standard dictionary word.
</prompt>

<success_criteria>
1. Each word N has exactly N letters
2. All words are valid English dictionary words
3. The paragraph is grammatically correct
4. The paragraph has coherent meaning
5. Scoring: count of consecutive correct words from the start

Scoring tiers:
- 8-10 words: Partial success
- 11-14 words: Good
- 15-17 words: Excellent  
- 18+: Exceptional (likely requires significant planning)
</success_criteria>

<verification_method>
```python
import enchant

def verify_fixed_point_paragraph(text):
    d = enchant.Dict("en_US")
    words = text.split()
    
    results = []
    consecutive_correct = 0
    failed = False
    
    for i, word in enumerate(words):
        target_length = i + 1
        # Strip punctuation for length check
        clean_word = ''.join(c for c in word if c.isalpha())
        actual_length = len(clean_word)
        is_valid_word = d.check(clean_word.lower())
        
        correct = (actual_length == target_length) and is_valid_word
        
        results.append({
            'position': i + 1,
            'word': word,
            'clean_word': clean_word,
            'target_length': target_length,
            'actual_length': actual_length,
            'is_dictionary_word': is_valid_word,
            'correct': correct
        })
        
        if correct and not failed:
            consecutive_correct += 1
        else:
            failed = True
    
    return {
        'results': results,
        'consecutive_correct': consecutive_correct,
        'total_words': len(words),
        'first_error_position': consecutive_correct + 1 if failed else None
    }

# Example test
text = "I am the best thing around becomes"
print(verify_fixed_point_paragraph(text))
```

Manual assessment required for: grammatical correctness, semantic coherence.
</verification_method>

<common_failures>
- Early failure: Wrong length word in positions 1-5 (often position 4 or 5)
- Dictionary failure: Using non-words to hit length targets
- Coherence collapse: Grammatically valid but meaningless after position 8-10
- Off-by-one: Miscounting letters in longer words
- Planning failure: Painting into a corner where no valid word exists
</common_failures>

<example_partial_success>
"I am the best thing around, perhaps thinking carefully." 
- Word 1: "I" (1) ✓
- Word 2: "am" (2) ✓
- Word 3: "the" (3) ✓
- Word 4: "best" (4) ✓
- Word 5: "thing" (5) ✓
- Word 6: "around" (6) ✓
- Word 7: "perhaps" (7) ✓
- Word 8: "thinking" (8) ✓
- Word 9: "carefully" (9) ✓
Score: 9 consecutive correct
</example_partial_success>

<why_this_is_hard>
You cannot write-then-verify because by the time you realize word 12 has no good options given words 10-11, you've already committed. Success requires:
1. Forward planning of word sequences
2. Awareness of what N-letter words exist for large N
3. Semantic planning to ensure coherence
4. Grammar planning to ensure validity

The constraint tightens dramatically as N increases because there are fewer long words and they must grammatically follow the previous context.
</why_this_is_hard>

<variants>
- Reverse: Start at word 15 (15 letters) and count down to 1
- Skip pattern: Words 1, 3, 5, 7... have matching lengths
- Two paragraphs: Ascending paragraph followed by descending paragraph
</variants>
</eval>

---

<eval id="EX-002">
<name>Mutually Describing Sentences</name>
<domain>Self-Reference / Fixed-Point Systems</domain>
<difficulty>Extreme</difficulty>

<prompt>
Create exactly three sentences labeled A, B, and C with these properties:

1. Sentence A must accurately state the total number of words in sentence B
2. Sentence B must accurately state the total number of characters in sentence C (including spaces and punctuation)
3. Sentence C must accurately state the combined count of the letter 'e' in sentences A and B together

Each sentence must:
- Be a complete, grammatically correct English sentence
- Make sense on its own (not just "B has 7 words")
- Sound natural, as if written by a human

Provide all three sentences and verify each constraint is satisfied.
</prompt>

<success_criteria>
1. Sentence A's stated word count matches B's actual word count
2. Sentence B's stated character count matches C's actual character count  
3. Sentence C's stated 'e' count matches the actual 'e' count in A+B combined
4. All three sentences are grammatically correct
5. All three sentences are semantically meaningful
6. The prose sounds natural (not robotic constraint-stating)
</success_criteria>

<verification_method>
```python
def verify_mutual_description(A, B, C):
    import re
    
    # Extract claimed numbers from each sentence
    def extract_number(s):
        numbers = re.findall(r'\b(\w+)\b', s.lower())
        word_to_num = {
            'zero': 0, 'one': 1, 'two': 2, 'three': 3, 'four': 4,
            'five': 5, 'six': 6, 'seven': 7, 'eight': 8, 'nine': 9,
            'ten': 10, 'eleven': 11, 'twelve': 12, 'thirteen': 13,
            'fourteen': 14, 'fifteen': 15, 'sixteen': 16, 'seventeen': 17,
            'eighteen': 18, 'nineteen': 19, 'twenty': 20
        }
        for word in numbers:
            if word in word_to_num:
                return word_to_num[word]
            if word.isdigit():
                return int(word)
        # Handle twenty-one, twenty-two, etc.
        match = re.search(r'twenty[- ](\w+)', s.lower())
        if match:
            ones = word_to_num.get(match.group(1), 0)
            return 20 + ones
        return None
    
    # Constraint 1: A states word count of B
    claimed_B_words = extract_number(A)
    actual_B_words = len(B.split())
    
    # Constraint 2: B states character count of C
    claimed_C_chars = extract_number(B)
    actual_C_chars = len(C)
    
    # Constraint 3: C states 'e' count of A+B
    claimed_AB_e = extract_number(C)
    actual_AB_e = (A + B).lower().count('e')
    
    return {
        'constraint_1': {
            'A_claims_B_has_words': claimed_B_words,
            'B_actual_words': actual_B_words,
            'satisfied': claimed_B_words == actual_B_words
        },
        'constraint_2': {
            'B_claims_C_has_chars': claimed_C_chars,
            'C_actual_chars': actual_C_chars,
            'satisfied': claimed_C_chars == actual_C_chars
        },
        'constraint_3': {
            'C_claims_AB_has_e': claimed_AB_e,
            'AB_actual_e': actual_AB_e,
            'satisfied': claimed_AB_e == actual_AB_e
        },
        'all_satisfied': (
            claimed_B_words == actual_B_words and
            claimed_C_chars == actual_C_chars and
            claimed_AB_e == actual_AB_e
        )
    }
```

Manual assessment required for: grammatical correctness, naturalness of prose.
</verification_method>

<common_failures>
- Circular dependency blindness: Not recognizing that changing A changes the 'e' count that C must report
- Off-by-one in character counting: Forgetting spaces or punctuation
- Number word trap: Using "seven" vs "7" changes the 'e' count
- Constraint isolation: Solving each independently then finding they conflict
- Unnatural prose: "The following sentence contains exactly twelve words."
</common_failures>

<why_this_is_hard>
This is a system of simultaneous equations with discrete, interdependent constraints. The key trap:
- If you write sentence A with the word "seven" (to describe B), that adds one 'e' to the A+B pool
- But if B has to change to satisfy constraint 2, that might change A's claim
- And C's statement about 'e's uses letters that are themselves part of the count

You need to find a fixed point where all three constraints stabilize.
</why_this_is_hard>

<variants>
- Four sentences: Add D that constrains A, making it fully circular
- Different properties: Word count, vowel count, capital letter count
- Harder numbers: Force solutions into higher number ranges where number words are longer
</variants>
</eval>

---

<eval id="EX-003">
<name>Verbal Rubik's Cube</name>
<domain>Spatial Simulation / 3D State Tracking</domain>
<difficulty>Extreme</difficulty>

<prompt>
A standard 3x3 Rubik's cube is in the solved state, with:
- White on top (U face)
- Yellow on bottom (D face)  
- Green on front (F face)
- Blue on back (B face)
- Red on right (R face)
- Orange on left (L face)

I apply the following sequence of moves: R U R' U R U2 R'

This is the "Sune" algorithm. After these moves are complete:

Question 1: What color is now showing on the TOP face of the front-right-top corner piece (the sticker you'd see looking down at the cube)?

Question 2: What color is now showing on the RIGHT face of that same front-right-top corner piece?

Question 3: What color is now showing on the FRONT face of that same front-right-top corner piece?

Show your reasoning by tracking the corner piece through each move.
</prompt>

<success_criteria>
1. Correct answer for Q1 (top face of FRU corner)
2. Correct answer for Q2 (right face of FRU corner)
3. Correct answer for Q3 (front face of FRU corner)
4. Reasoning demonstrates actual move-by-move tracking (not guessing)
</success_criteria>

<verification_method>
Physical verification: Perform the moves on an actual cube.

Or use an online simulator: https://alg.cubing.net/?alg=R_U_R-_U_R_U2_R-

Starting position of FRU corner (front-right-top):
- Top sticker: White
- Front sticker: Green  
- Right sticker: Red

After R U R' U R U2 R':
- The FRU corner piece has cycled with other corners
- Track which physical piece is now at FRU position
- Report the colors of that piece's stickers

CORRECT ANSWERS (verify with cube):
After Sune, the FRU corner shows:
- Top: [verify]
- Right: [verify]
- Front: [verify]

Note: The solver should verify these with an actual cube before using this eval.
</verification_method>

<common_failures>
- Face vs piece confusion: Confusing which face of the cube vs which sticker of the piece
- Move notation errors: R' means counterclockwise, not clockwise
- Lost tracking: Losing track of the piece after 3-4 moves
- Pattern matching: Saying "Sune twists corners" without actually simulating
- Orientation vs position: Confusing where the piece is vs how it's rotated
</common_failures>

<why_this_is_hard>
Models have seen descriptions of Rubik's cube algorithms, but not typically the effect of specific algorithms on specific stickers. This requires:
1. Understanding 3D cube geometry
2. Correctly interpreting move notation
3. Tracking a specific piece through 7 sequential transformations
4. Distinguishing piece position from piece orientation

There's no shortcut—you must simulate the actual moves.
</why_this_is_hard>

<variants>
- Different algorithm: Use T-perm, J-perm, or other algorithms
- Edge tracking: Track an edge piece instead of a corner
- Full state: "After these moves, describe the entire top face"
- Inverse: "What algorithm would move the green-red-white piece to position XYZ?"
</variants>
</eval>

---

<eval id="EX-004">
<name>Steganographic Review</name>
<domain>Constrained Generation / Steganography</domain>
<difficulty>Extreme</difficulty>

<prompt>
Write a product review for a wireless Bluetooth speaker that:

1. Sounds like a genuine, enthusiastic 5-star review
2. Is at least 150 words
3. Contains exactly 13 sentences
4. The first letter of each sentence, read in order, spells out: "DONT BUY THIS"

The review must:
- Sound authentically positive and helpful
- Include specific (invented) details about features, sound quality, etc.
- Not contain any obvious hints that there's a hidden message
- Flow naturally from sentence to sentence
</prompt>

<success_criteria>
1. First letters spell "DONT BUY THIS" (13 letters, 13 sentences)
2. Review is at least 150 words
3. Review reads as genuinely positive/5-star
4. Review contains specific product details
5. No obvious tells that reveal the hidden message
6. Sentences flow naturally (not forced transitions)
</success_criteria>

<verification_method>
```python
def verify_steganographic_review(text):
    import re
    
    # Split into sentences
    sentences = re.split(r'(?<=[.!?])\s+', text.strip())
    
    # Extract first letters
    first_letters = ''.join(s[0].upper() for s in sentences if s)
    target = "DONTBUYTHIS"
    
    # Word count
    word_count = len(text.split())
    
    return {
        'sentence_count': len(sentences),
        'first_letters': first_letters,
        'target_message': target,
        'message_match': first_letters == target,
        'word_count': word_count,
        'meets_word_minimum': word_count >= 150,
        'sentences': [{'index': i, 'first_letter': s[0] if s else '', 'text': s[:50]+'...'} 
                      for i, s in enumerate(sentences)]
    }
```

Manual assessment required for: 
- Does it sound genuinely positive?
- Are there obvious tells?
- Do sentences flow naturally?
- Are product details plausible?
</verification_method>

<common_failures>
- Wrong sentence count: 12 or 14 sentences instead of 13
- Forced starts: "Dare I say this is the best speaker!" (unnatural D-start)
- Sentiment leakage: Negative words slipping in despite positive framing
- Transition failures: "Now, let me tell you about..." for every N-start
- Letter mismatch: Wrong letter due to miscounting sentences
- Quality collapse: Technically correct but obviously AI-generated
</common_failures>

<example_sentence_starts>
- D: "Delighted doesn't begin to describe..."
- O: "Outstanding bass response..."
- N: "Nothing compares to..."
- T: "The build quality..."
- B: "Battery life exceeds..."
- U: "Unbelievably clear..."
- Y: "You'll appreciate..."
- T: "Truly portable..."
- H: "Having used many speakers..."
- I: "I've recommended this to..."
- S: "Sound quality rivals..."
</example_sentence_starts>

<why_this_is_hard>
Each sentence has three simultaneous constraints:
1. Must start with a specific letter
2. Must maintain positive sentiment
3. Must flow from the previous sentence

Some letters are hard to start positive sentences with naturally (Y, U, H). The model must also maintain a consistent "voice" throughout while hitting exact marks.
</why_this_is_hard>

<variants>
- Longer message: "HELP I AM TRAPPED IN A REVIEW FACTORY" (requires 26 sentences)
- Different product: Restaurant review, movie review, book review
- Different encoding: Last letter of each sentence, or first letter of sentences 1, 3, 5, 7...
- Adversarial: Hidden negative message in positive review (as shown) or hidden positive in negative
</variants>
</eval>

---

<eval id="EX-005">
<name>Counting Constraint Cascade</name>
<domain>Multi-Constraint Satisfaction</domain>
<difficulty>Extreme</difficulty>

<prompt>
Write a single English sentence that satisfies ALL of the following constraints simultaneously:

1. Contains exactly 14 words
2. Contains exactly 70 characters (including spaces and punctuation)
3. Contains exactly 3 words that start with a vowel (a, e, i, o, u)
4. Contains exactly 5 words that are longer than 5 letters
5. Contains exactly 2 commas
6. The first and last words have the same number of letters

Provide the sentence and verify each constraint.
</prompt>

<success_criteria>
All six constraints must be satisfied:
1. Word count = 14
2. Character count = 70
3. Vowel-start words = 3
4. Words with >5 letters = 5
5. Comma count = 2
6. First word length = Last word length
</success_criteria>

<verification_method>
```python
def verify_constraint_cascade(sentence):
    words = sentence.replace(',', ' ').replace('.', ' ').split()
    # More precise word extraction
    import re
    words = re.findall(r"[a-zA-Z]+", sentence)
    
    # Constraint 1: Word count
    word_count = len(words)
    
    # Constraint 2: Character count (including spaces and punctuation)
    char_count = len(sentence)
    
    # Constraint 3: Words starting with vowels
    vowel_starts = sum(1 for w in words if w[0].lower() in 'aeiou')
    
    # Constraint 4: Words longer than 5 letters
    long_words = sum(1 for w in words if len(w) > 5)
    
    # Constraint 5: Comma count
    comma_count = sentence.count(',')
    
    # Constraint 6: First and last word same length
    first_len = len(words[0])
    last_len = len(words[-1])
    same_length = first_len == last_len
    
    results = {
        'word_count': {'actual': word_count, 'target': 14, 'pass': word_count == 14},
        'char_count': {'actual': char_count, 'target': 70, 'pass': char_count == 70},
        'vowel_starts': {'actual': vowel_starts, 'target': 3, 'pass': vowel_starts == 3},
        'long_words': {'actual': long_words, 'target': 5, 'pass': long_words == 5},
        'comma_count': {'actual': comma_count, 'target': 2, 'pass': comma_count == 2},
        'first_last_length': {
            'first': first_len, 
            'last': last_len, 
            'pass': same_length
        },
        'all_pass': all([
            word_count == 14,
            char_count == 70,
            vowel_starts == 3,
            long_words == 5,
            comma_count == 2,
            same_length
        ])
    }
    return results
```
</verification_method>

<common_failures>
- Near misses: 5 constraints satisfied, 1 off by one
- Character count errors: Miscounting spaces or punctuation
- Word boundary confusion: Hyphenated words, contractions
- Constraint interaction blindness: Fixing one constraint breaks another
- Brute force failure: Unable to search the space systematically
</common_failures>

<why_this_is_hard>
Each constraint alone is trivial. But they interact:
- Adding a word to hit 14 words changes character count
- Changing a word to hit the character target might change vowel-start count
- Adding commas adds characters but not words
- Making first/last words match length constrains word choice at both ends

The constraints form a tightly coupled system with a small valid solution space.
</why_this_is_hard>

<solution_existence>
Before using this eval, verify that a solution exists. Here's one approach:
- 14 words, 2 commas means 14 words + 2 commas + spaces = 70 chars
- 13 spaces between words + 2 commas = 15 punctuation/space chars
- So word letters must sum to 55
- Average word length: 55/14 ≈ 3.9 letters
- Need 5 words > 5 letters, so 5 words averaging ~6.5 letters = 32.5
- Remaining 9 words average ~2.5 letters = 22.5. Total = 55 ✓
- This is feasible but tight.
</solution_existence>

<variants>
- Relaxed: Reduce to 4 constraints
- Tightened: Add constraint "no word appears twice"
- Different constraints: Syllable count, specific letter frequency
</variants>
</eval>

---

<eval id="EX-006">
<name>Pathological Logic Grid</name>
<domain>Logic / Adversarial Constraint Satisfaction</domain>
<difficulty>Extreme</difficulty>

<prompt>
Five friends—Alice, Bob, Carol, Dave, and Eve—each have a different favorite color (blue, green, red, white, yellow) and a different favorite number (1, 2, 3, 4, 5).

Determine each person's favorite color and number from these clues:

1. The person who likes blue has a favorite number that is one less than Carol's favorite number.
2. Alice's favorite number is higher than the number liked by the person who likes green.
3. Bob doesn't like yellow or green.
4. Eve's favorite number is 3.
5. The person who likes red has favorite number 5.
6. Dave's favorite number is lower than Bob's.
7. Carol doesn't like blue.
8. The person who likes white has a favorite number exactly 2 less than Alice's.
9. Bob's favorite number is not 4.
10. Dave doesn't like red or white.

Provide the complete solution showing each person's color and number. Show your reasoning, including any deductions you tried that led to contradictions.
</prompt>

<success_criteria>
1. Correct final assignment (each person has one color and one number)
2. All 10 clues are satisfied by the solution
3. Reasoning shows systematic deduction
4. Reasoning includes at least one path that was abandoned due to contradiction
</success_criteria>

<verification_method>
```python
def verify_logic_grid(solution):
    """
    solution = {
        'Alice': {'color': 'X', 'number': N},
        'Bob': {'color': 'X', 'number': N},
        ...
    }
    """
    # Helper lookups
    by_color = {v['color']: k for k, v in solution.items()}
    by_number = {v['number']: k for k, v in solution.items()}
    
    def get_color(person):
        return solution[person]['color']
    
    def get_number(person):
        return solution[person]['number']
    
    def person_with_color(color):
        return by_color.get(color)
    
    def person_with_number(num):
        return by_number.get(num)
    
    clues = []
    
    # Clue 1: Blue person's number = Carol's number - 1
    blue_person = person_with_color('blue')
    c1 = get_number(blue_person) == get_number('Carol') - 1
    clues.append(('Clue 1', c1))
    
    # Clue 2: Alice's number > green person's number
    green_person = person_with_color('green')
    c2 = get_number('Alice') > get_number(green_person)
    clues.append(('Clue 2', c2))
    
    # Clue 3: Bob doesn't like yellow or green
    c3 = get_color('Bob') not in ['yellow', 'green']
    clues.append(('Clue 3', c3))
    
    # Clue 4: Eve's number is 3
    c4 = get_number('Eve') == 3
    clues.append(('Clue 4', c4))
    
    # Clue 5: Red person has number 5
    red_person = person_with_color('red')
    c5 = get_number(red_person) == 5
    clues.append(('Clue 5', c5))
    
    # Clue 6: Dave's number < Bob's number
    c6 = get_number('Dave') < get_number('Bob')
    clues.append(('Clue 6', c6))
    
    # Clue 7: Carol doesn't like blue
    c7 = get_color('Carol') != 'blue'
    clues.append(('Clue 7', c7))
    
    # Clue 8: White person's number = Alice's number - 2
    white_person = person_with_color('white')
    c8 = get_number(white_person) == get_number('Alice') - 2
    clues.append(('Clue 8', c8))
    
    # Clue 9: Bob's number is not 4
    c9 = get_number('Bob') != 4
    clues.append(('Clue 9', c9))
    
    # Clue 10: Dave doesn't like red or white
    c10 = get_color('Dave') not in ['red', 'white']
    clues.append(('Clue 10', c10))
    
    return {
        'clue_results': clues,
        'all_satisfied': all(result for _, result in clues)
    }

# Brute force solver to find valid solution(s)
from itertools import permutations

def solve_logic_grid():
    people = ['Alice', 'Bob', 'Carol', 'Dave', 'Eve']
    colors = ['blue', 'green', 'red', 'white', 'yellow']
    numbers = [1, 2, 3, 4, 5]
    
    solutions = []
    
    for color_perm in permutations(colors):
        for number_perm in permutations(numbers):
            solution = {
                p: {'color': c, 'number': n}
                for p, c, n in zip(people, color_perm, number_perm)
            }
            
            result = verify_logic_grid(solution)
            if result['all_satisfied']:
                solutions.append(solution)
    
    return solutions

# Run this to find the correct answer(s)
print(solve_logic_grid())
```

IMPORTANT: Run the solver before using this eval to confirm:
1. Exactly one solution exists
2. The puzzle requires backtracking (early obvious deductions lead to contradiction)
</verification_method>

<design_notes>
This puzzle is specifically constructed so that:
- Clue 4 (Eve = 3) seems like a great starting point
- Clue 5 (red = 5) combined with clue 6 (Dave < Bob) suggests Dave isn't red
- But following the "obvious" path from these clues leads to a contradiction at clue 8
- The solver must backtrack and try a less obvious branch

If testing reveals this puzzle doesn't have these properties, modify clues to create the adversarial structure.
</design_notes>

<common_failures>
- Premature commitment: Assuming an early inference is certain
- No backtracking: Getting stuck rather than revising
- Incomplete search: Missing the valid solution because wrong branch was explored
- Logical errors: Misinterpreting clue language ("one less than" direction)
</common_failures>

<variants>
- Larger grid: 6 people, 3 attributes each
- More adversarial: Multiple points requiring backtracking
- Red herring clues: Include clues that are true but not useful for deduction
</variants>
</eval>

---

<eval id="EX-007">
<name>Precise Bitmap</name>
<domain>Spatial Generation / Counting</domain>
<difficulty>Extreme</difficulty>

<prompt>
Output a 12×12 grid of 0s and 1s that forms a recognizable heart shape. The grid must contain EXACTLY 40 ones (1s).

Format your answer as 12 rows of 12 characters each, using only 0 and 1, with spaces between characters for readability.

The heart should:
- Be clearly recognizable as a heart shape
- Be roughly centered in the grid
- Use exactly 40 ones (no more, no less)
</prompt>

<success_criteria>
1. Grid is exactly 12×12
2. Contains only 0s and 1s
3. Exactly 40 ones total
4. Shape is recognizable as a heart (manual assessment)
5. Heart is roughly centered
</success_criteria>

<verification_method>
```python
def verify_bitmap(grid_text):
    # Parse the grid
    lines = [line.strip() for line in grid_text.strip().split('\n') if line.strip()]
    
    # Remove spaces within lines
    grid = []
    for line in lines:
        row = [c for c in line if c in '01']
        grid.append(row)
    
    # Check dimensions
    height = len(grid)
    width = len(grid[0]) if grid else 0
    consistent_width = all(len(row) == width for row in grid)
    
    # Count ones
    ones_count = sum(row.count('1') for row in grid)
    
    # Visual representation
    visual = '\n'.join([''.join('█' if c == '1' else '·' for c in row) for row in grid])
    
    return {
        'dimensions': f'{height}x{width}',
        'correct_dimensions': height == 12 and width == 12 and consistent_width,
        'ones_count': ones_count,
        'correct_count': ones_count == 40,
        'visual': visual,
        'grid': grid
    }
```

Manual assessment for: Is the shape recognizable as a heart?
</verification_method>

<reference_heart_shape>
A valid 12x12 heart with 40 ones might look approximately like:
```
0 0 1 1 0 0 0 0 1 1 0 0
0 1 1 1 1 0 0 1 1 1 1 0
1 1 1 1 1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1 1 1 1 1
1 1 1 1 1 1 1 1 1 1 1 1
0 1 1 1 1 1 1 1 1 1 1 0
0 0 1 1 1 1 1 1 1 1 0 0
0 0 0 1 1 1 1 1 1 0 0 0
0 0 0 0 1 1 1 1 0 0 0 0
0 0 0 0 0 1 1 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0
0 0 0 0 0 0 0 0 0 0 0 0
```
(Count to verify this has exactly 40 ones—adjust if not)
</reference_heart_shape>

<common_failures>
- Wrong count: Correct shape but 38 or 42 ones
- Wrong dimensions: 10x10 or 14x14 grid
- Unrecognizable shape: Right count but doesn't look like a heart
- Asymmetric: Heart is lopsided
- Off-center: Heart pushed to one corner
- Mixed errors: Wrong count AND poor shape
</common_failures>

<why_this_is_hard>
The model must simultaneously:
1. Hold a mental model of a heart shape
2. Translate that to a discrete grid
3. Count cells while drawing
4. Adjust the shape to hit exactly 40

There's no way to verify until the entire grid is complete, but by then it's too late to easily adjust.
</why_this_is_hard>

<variants>
- Different shapes: Arrow (→), star, letter "A"
- Different counts: Exactly 50 ones, or exactly 25% of cells
- Larger grid: 20×20 with exactly 100 ones
- Multiple shapes: Grid contains both a heart AND a star, together using exactly 60 ones
</variants>
</eval>

---

<eval id="EX-008">
<name>Interleaved Dual Narrative</name>
<domain>Parallel Generation / Coherence</domain>
<difficulty>Extreme</difficulty>

<prompt>
Write exactly 12 sentences, numbered 1-12.

When you read only the ODD-numbered sentences (1, 3, 5, 7, 9, 11) in order, they form a coherent short story about a detective solving a mystery.

When you read only the EVEN-numbered sentences (2, 4, 6, 8, 10, 12) in order, they form a coherent short story about a chef preparing for a important dinner.

Requirements:
- Each sub-story (odd and even) must make sense as a standalone narrative
- Each sub-story must have a clear beginning, middle, and end
- The transition from sentence N to sentence N+2 must be natural within each story
- Reading all 12 sentences together can be disjointed (that's expected)
</prompt>

<success_criteria>
1. Exactly 12 sentences provided
2. Odd sentences (1,3,5,7,9,11) form coherent detective story
3. Even sentences (2,4,6,8,10,12) form coherent chef story
4. Each story has narrative arc (beginning, middle, end)
5. Within-story transitions are natural
6. Stories match their assigned topics (detective/chef)
</success_criteria>

<verification_method>
```python
def verify_dual_narrative(text):
    import re
    
    # Extract numbered sentences
    sentences = {}
    pattern = r'(\d+)[.):]\s*(.+?)(?=\d+[.):]\s|$)'
    matches = re.findall(pattern, text, re.DOTALL)
    
    for num, content in matches:
        sentences[int(num)] = content.strip()
    
    # Split into odd and even
    odd_sentences = [sentences.get(i, '') for i in [1,3,5,7,9,11]]
    even_sentences = [sentences.get(i, '') for i in [2,4,6,8,10,12]]
    
    detective_story = ' '.join(odd_sentences)
    chef_story = ' '.join(even_sentences)
    
    return {
        'sentence_count': len(sentences),
        'has_all_12': all(i in sentences for i in range(1, 13)),
        'detective_story': detective_story,
        'chef_story': chef_story,
        'odd_sentences': odd_sentences,
        'even_sentences': even_sentences
    }
```

Manual assessment required for:
- Is the detective story coherent and complete?
- Is the chef story coherent and complete?  
- Are within-story transitions natural?
- Do stories have proper narrative arcs?
</verification_method>

<common_failures>
- Story bleed: Detective elements appearing in chef sentences or vice versa
- Arc failure: One or both stories lack clear beginning/middle/end
- Transition jarring: Sentence 5 doesn't follow naturally from sentence 3
- Topic drift: Stories wander off their assigned topics
- Coherence collapse: Stories become incoherent by the end
- Length imbalance: One story is developed, the other is skeletal
</common_failures>

<why_this_is_hard>
You're writing two interleaved stories simultaneously. This requires:
1. Maintaining two separate narrative states in parallel
2. Planning ahead so sentence N fits with both N-1 (different story) and N-2 (same story)
3. Ensuring both stories independently have satisfying arcs
4. Keeping topics clearly separated despite alternation

The cognitive load of tracking two stories while ensuring each advances properly is substantial.
</why_this_is_hard>

<variants>
- Three stories: Sentences 1,4,7,10 / 2,5,8,11 / 3,6,9,12
- Same genre: Both stories are mysteries (harder to keep separate)
- Character crossover: Same character appears in both (as different roles)
- Longer form: 20 sentences (10 per story)
</variants>
</eval>

---

<eval id="EX-009">
<name>Unbounded Calendar Chain</name>
<domain>Temporal Reasoning / Chained Calculation</domain>
<difficulty>Extreme</difficulty>

<prompt>
January 1, 2030 is a Wednesday.

Without using any tools, calculators, or external references, determine the answer to this question:

What day of the week is the date that falls 100 days AFTER the third Monday that occurs AFTER the last Friday of the month containing the 200th day of 2030?

Show all your work, including:
1. Finding the 200th day of 2030
2. Identifying what month contains it
3. Finding the last Friday of that month
4. Finding the third Monday after that Friday
5. Adding 100 days
6. Determining the day of the week
</prompt>

<success_criteria>
1. Correct identification of day 200 (which date and month)
2. Correct last Friday of that month
3. Correct third Monday after that Friday
4. Correct date 100 days later
5. Correct final day of week
6. Work shown for each step
</success_criteria>

<verification_method>
```python
from datetime import date, timedelta

def solve_calendar_chain():
    # Step 1: Find day 200 of 2030
    jan1 = date(2030, 1, 1)
    day_200 = jan1 + timedelta(days=199)  # day 1 is Jan 1, so day 200 is +199
    
    print(f"Day 200 of 2030: {day_200} ({day_200.strftime('%A')})")
    print(f"Month containing day 200: {day_200.strftime('%B')}")
    
    # Step 2: Find last Friday of that month
    # Get the last day of the month
    if day_200.month == 12:
        last_of_month = date(2030, 12, 31)
    else:
        last_of_month = date(2030, day_200.month + 1, 1) - timedelta(days=1)
    
    # Find last Friday
    days_since_friday = (last_of_month.weekday() - 4) % 7
    last_friday = last_of_month - timedelta(days=days_since_friday)
    
    print(f"Last day of {day_200.strftime('%B')}: {last_of_month}")
    print(f"Last Friday of {day_200.strftime('%B')}: {last_friday}")
    
    # Step 3: Find third Monday after that Friday
    # First Monday after the Friday
    days_to_monday = (7 - last_friday.weekday()) % 7
    if days_to_monday == 0:
        days_to_monday = 7  # If Friday, next Monday is in 3 days... wait, let me recalc
    # Friday is weekday 4, Monday is weekday 0
    # Days from Friday to next Monday = (0 - 4) % 7 = 3? No...
    # (7 - 4 + 0) % 7 = 3. So Friday + 3 = Monday. That's the FIRST Monday after.
    # Third Monday after = first Monday + 14 days
    
    days_to_first_monday = (7 - last_friday.weekday() + 0) % 7
    if days_to_first_monday == 0:
        days_to_first_monday = 7
    first_monday_after = last_friday + timedelta(days=days_to_first_monday)
    third_monday_after = first_monday_after + timedelta(days=14)
    
    print(f"First Monday after {last_friday}: {first_monday_after}")
    print(f"Third Monday after {last_friday}: {third_monday_after}")
    
    # Step 4: Add 100 days
    final_date = third_monday_after + timedelta(days=100)
    
    print(f"100 days after {third_monday_after}: {final_date}")
    print(f"Day of week: {final_date.strftime('%A')}")
    
    # Verify Jan 1, 2030 is Wednesday
    print(f"\nVerification: Jan 1, 2030 is {jan1.strftime('%A')}")
    
    return final_date.strftime('%A')

print(solve_calendar_chain())
```

Run this to get the correct answer before using as an eval.
</verification_method>

<common_failures>
- Day 200 error: Off-by-one on which date is day 200
- Month boundary: Wrong month for day 200
- Last Friday calculation: Wrong Friday (often off by a week)
- "After" interpretation: Does "third Monday after Friday" include a Monday that IS the Friday? (No)
- Cumulative error: Small errors compound through the chain
- Day-of-week calculation: Final modular arithmetic error
</common_failures>

<why_this_is_hard>
This chains 5+ non-trivial calculations where each depends on the previous. There's no shortcut—you must:
1. Know days in each month
2. Track cumulative days correctly
3. Understand weekday arithmetic
4. Handle "Nth X after Y" precisely
5. Not lose track through the chain

Each step is moderately hard; the chain makes it very hard.
</why_this_is_hard>

<variants>
- Longer chain: Add more steps ("the second Thursday before...")
- Leap year: Use 2028 or 2032 where February has 29 days
- Different starting point: Start from a non-January date
- Backward chaining: "What date is 50 days BEFORE the..."
</variants>
</eval>

---

<eval id="EX-010">
<name>Self-Counting Story</name>
<domain>Self-Reference / Fixed-Point Generation</domain>
<difficulty>Extreme</difficulty>

<prompt>
Write a short story of EXACTLY 100 words. The story can be about any topic, but it must satisfy this constraint:

The very last sentence of the story must truthfully state how many times the letter 'a' (case-insensitive) appears in the ENTIRE story, including that final sentence itself.

For example, if your story contains 47 letter a's total (including the a's in your final sentence "This story contains forty-seven a's"), then your final sentence must state "forty-seven" (or the appropriate number).

The story should:
- Be exactly 100 words
- Have the final sentence be a natural part of the narrative (not just "This story has N a's")
- Have the count in the final sentence be correct
</prompt>

<success_criteria>
1. Story is exactly 100 words
2. Final sentence states a number
3. That number equals the actual count of 'a' in the entire story
4. The final sentence feels like part of the narrative
5. Story is coherent and readable
</success_criteria>

<verification_method>
```python
def verify_self_counting_story(text):
    import re
    
    # Word count
    words = text.split()
    word_count = len(words)
    
    # Count letter 'a' (case-insensitive)
    a_count = text.lower().count('a')
    
    # Extract the claimed number from the last sentence
    sentences = re.split(r'[.!?]+', text)
    sentences = [s.strip() for s in sentences if s.strip()]
    last_sentence = sentences[-1] if sentences else ""
    
    # Try to extract number from last sentence
    word_to_num = {
        'zero': 0, 'one': 1, 'two': 2, 'three': 3, 'four': 4, 'five': 5,
        'six': 6, 'seven': 7, 'eight': 8, 'nine': 9, 'ten': 10,
        'eleven': 11, 'twelve': 12, 'thirteen': 13, 'fourteen': 14,
        'fifteen': 15, 'sixteen': 16, 'seventeen': 17, 'eighteen': 18,
        'nineteen': 19, 'twenty': 20, 'thirty': 30, 'forty': 40,
        'fifty': 50, 'sixty': 60, 'seventy': 70, 'eighty': 80, 'ninety': 90
    }
    
    claimed = None
    # Check for digit
    digit_match = re.search(r'\b(\d+)\b', last_sentence)
    if digit_match:
        claimed = int(digit_match.group(1))
    else:
        # Check for word numbers (simplified)
        lower_last = last_sentence.lower()
        for word, num in word_to_num.items():
            if word in lower_last:
                # Handle compounds like "forty-seven"
                compound_match = re.search(rf'{word}[- ](\w+)', lower_last)
                if compound_match:
                    ones_word = compound_match.group(1)
                    if ones_word in word_to_num:
                        claimed = num + word_to_num[ones_word]
                        break
                else:
                    claimed = num
    
    return {
        'word_count': word_count,
        'correct_word_count': word_count == 100,
        'actual_a_count': a_count,
        'claimed_a_count': claimed,
        'count_matches': claimed == a_count,
        'last_sentence': last_sentence,
        'all_pass': word_count == 100 and claimed == a_count
    }
```

Manual assessment required for: narrative quality, natural integration of final sentence.
</verification_method>

<common_failures>
- Bootstrap failure: The claimed number affects the actual count
  - "forty" has 1 'a', "fifty" has 0 'a's, "thirty" has 0 'a's
  - Changing the number word changes the count
- Word count error: 98 or 102 words instead of 100
- Count error: Miscounting 'a's (especially with many 'a's)
- Unnatural final sentence: "This story contains forty-two a's" doesn't fit narrative
- Fixed-point miss: Can't find a number N where spelling N gives exactly N 'a's total
</common_failures>

<why_this_is_hard>
This is a constraint satisfaction problem with self-reference:
- You need exactly 100 words
- The final sentence must state N
- The story must contain exactly N 'a's
- But the spelling of N contributes to the 'a' count

If your story (excluding final sentence) has 45 'a's, and you write "forty-six" (1 'a'), total is 46. ✓
If your story has 44 'a's and you write "forty-four" (1 'a'), total is 45. ✗

You must find a fixed point where story_a's + number_word_a's = stated_number.
</why_this_is_hard>

<number_word_a_counts>
For planning, here are 'a' counts in number words:
- 0-10: zero(0), one(0), two(0), three(0), four(0), five(0), six(0), seven(0), eight(0), nine(0), ten(0)
- 11-20: eleven(0), twelve(0), thirteen(0), fourteen(0), fifteen(0), sixteen(0), seventeen(0), eighteen(0), nineteen(0), twenty(0)
- Tens: thirty(0), forty(0), fifty(0), sixty(0), seventy(0), eighty(0), ninety(0)
- Compounds: forty-one(0), forty-two(0), ..., 

Wait, let me recount:
- "a" appears in: "a" itself
- forty-seven: f-o-r-t-y-s-e-v-e-n → no 'a'
- Actually most number words don't have 'a' in them!

Exceptions with 'a':
- "a" in: eight(0), one(0)... actually none have 'a'!
- Wait: "a" appears in... let me check each:
  - one, two, three, four, five, six, seven, eight, nine, ten: none have 'a'
  - eleven through nineteen: none have 'a'
  - twenty, thirty, forty, fifty, sixty, seventy, eighty, ninety: none have 'a'
  
So number words contribute 0 'a's (in the number itself).

But "a's" or "letter a" in the final sentence would add 'a's!
</number_word_a_counts>

<variants>
- Different letter: Count 'e' (which appears in many number words: seven, eleven, twelve, thirteen, etc.)
- Different word count: 50 words or 150 words
- Multiple constraints: Final sentence states both 'a' count AND word count accurately
</variants>
</eval>

---

# Tier 2: Very Hard

---

<eval id="VH-001">
<name>Self-Counting Sentence (Autogram)</name>
<domain>Self-Reference / Constraint Satisfaction</domain>
<difficulty>Very Hard</difficulty>

<prompt>
Write a sentence in English that accurately counts how many of each letter it contains. The sentence should be in the form: "This sentence contains [number] a's, [number] b's, [number] c's, ..." and so on for every letter that appears at least once. The count must be perfectly accurate—the sentence must describe itself correctly.

You may choose which letters to include (you don't need all 26), but every letter you mention must have the correct count, and every letter that appears in the sentence must be mentioned.
</prompt>

<success_criteria>
1. Sentence is self-consistent: every letter count mentioned is accurate
2. Every letter appearing in the sentence is accounted for
3. The sentence is grammatically valid English
4. Numbers are spelled out as words (not digits)
</success_criteria>

<verification_method>
```python
from collections import Counter
import re

def verify_autogram(sentence):
    # Count actual letters
    actual_counts = Counter(c.lower() for c in sentence if c.isalpha())
    
    # Parse claimed counts
    word_to_num = {
        'one': 1, 'two': 2, 'three': 3, 'four': 4, 'five': 5,
        'six': 6, 'seven': 7, 'eight': 8, 'nine': 9, 'ten': 10,
        'eleven': 11, 'twelve': 12, 'thirteen': 13, 'fourteen': 14,
        'fifteen': 15, 'sixteen': 16, 'seventeen': 17, 'eighteen': 18,
        'nineteen': 19, 'twenty': 20, 'twenty-one': 21, 'twenty-two': 22,
        'twenty-three': 23, 'twenty-four': 24, 'twenty-five': 25,
        'twenty-six': 26, 'twenty-seven': 27, 'twenty-eight': 28,
        'twenty-nine': 29, 'thirty': 30
    }
    
    # Find patterns like "three a's" or "three as"
    pattern = r"(\w+(?:-\w+)?)\s+([a-z])(?:'s|s)?"
    claimed = {}
    for match in re.finditer(pattern, sentence.lower()):
        num_word, letter = match.groups()
        if num_word in word_to_num:
            claimed[letter] = word_to_num[num_word]
    
    # Compare
    errors = []
    for letter, claimed_count in claimed.items():
        actual = actual_counts.get(letter, 0)
        if actual != claimed_count:
            errors.append({
                'letter': letter,
                'claimed': claimed_count,
                'actual': actual
            })
    
    # Check for unmentioned letters
    for letter in actual_counts:
        if letter not in claimed:
            errors.append({
                'letter': letter,
                'claimed': 'not mentioned',
                'actual': actual_counts[letter]
            })
    
    return {
        'errors': errors,
        'pass': len(errors) == 0,
        'actual_counts': dict(actual_counts),
        'claimed_counts': claimed
    }
```
</verification_method>

<common_failures>
- Bootstrap paradox: changing a count word changes the letter frequencies
- Iterative non-convergence: attempts to fix errors introduce new ones
- Missing self-reference: forgets to count letters in the count words themselves
</common_failures>

<known_valid_example>
"This sentence employs two a's, two c's, two d's, twenty-eight e's, five f's, three g's, eight h's, eleven i's, three l's, two m's, thirteen n's, nine o's, two p's, five r's, twenty-five s's, twenty-three t's, six v's, ten w's, two x's, five y's, and one z."

(Verify before using—this is a famous example that should be checked)
</known_valid_example>
</eval>

---

<eval id="VH-002">
<name>Reverse Quine</name>
<domain>Self-Reference / Metaprogramming</domain>
<difficulty>Very Hard</difficulty>

<prompt>
Write a Python program that, when executed, prints its own source code reversed (character by character). 

For example, if your program is `print("hello")`, it should output `)\"olleh\"(tnirp`.

The program must:
1. Be valid Python that runs without errors
2. Print exactly its own source code reversed
3. Not read from its own source file (no `open(__file__)` or similar)
4. Not use the `inspect` module
5. Contain at least 10 characters
</prompt>

<success_criteria>
1. Program runs without error in Python 3.x
2. Output is exactly the program source reversed character by character
3. No file I/O used
4. No inspect module used
5. Program is at least 10 characters
</success_criteria>

<verification_method>
```python
def verify_reverse_quine(source_code):
    import subprocess
    import tempfile
    import os
    
    # Check for forbidden patterns
    forbidden = ['__file__', 'inspect', 'open(']
    for pattern in forbidden:
        if pattern in source_code:
            return {'pass': False, 'reason': f'Forbidden pattern: {pattern}'}
    
    # Check minimum length
    if len(source_code) < 10:
        return {'pass': False, 'reason': 'Too short'}
    
    # Write and execute
    with tempfile.NamedTemporaryFile(mode='w', suffix='.py', delete=False) as f:
        f.write(source_code)
        temp_path = f.name
    
    try:
        result = subprocess.run(
            ['python3', temp_path],
            capture_output=True,
            text=True,
            timeout=5
        )
        
        output = result.stdout
        expected = source_code[::-1]
        
        return {
            'runs': result.returncode == 0,
            'output': repr(output),
            'expected': repr(expected),
            'match': output == expected,
            'stderr': result.stderr,
            'pass': result.returncode == 0 and output == expected
        }
    finally:
        os.unlink(temp_path)
```
</verification_method>

<common_failures>
- Incorrect reversal: off-by-one or missing characters
- Quote escaping: not handling quotes properly in the output
- Newline issues: trailing newline handling
- Forbidden methods: using file reading or inspect
</common_failures>
</eval>

---

<eval id="VH-003">
<name>Self-Referential Crossword</name>
<domain>Puzzles / Self-Reference</domain>
<difficulty>Very Hard</difficulty>

<prompt>
Create a valid 5x5 crossword puzzle where:

1. All 25 cells are filled (no black squares)
2. All 10 words (5 across, 5 down) are valid English words
3. Include this self-referential clue structure:
   - The clue for 1-Across is: "An anagram of 3-Down"
   - The answer to 1-Across must actually be an anagram of the answer to 3-Down

Provide:
1. The completed 5x5 grid
2. All 10 clues (5 Across, 5 Down)
3. Verification that 1-Across is an anagram of 3-Down
</prompt>

<success_criteria>
1. 5x5 grid fully filled
2. All 10 words are valid English dictionary words
3. 1-Across is an anagram of 3-Down
4. All other clues accurately describe their answers
5. Grid is consistent (intersecting words share letters correctly)
</success_criteria>

<verification_method>
```python
import enchant

def verify_crossword(grid, clues):
    d = enchant.Dict("en_US")
    
    # Grid is 5x5 list of lists
    # Extract words
    across = [''.join(grid[i]) for i in range(5)]
    down = [''.join(grid[i][j] for i in range(5)) for j in range(5)]
    
    # Check all words are valid
    all_words = across + down
    validity = {word: d.check(word.lower()) for word in all_words}
    
    # Check anagram constraint
    one_across = across[0]
    three_down = down[2]
    is_anagram = sorted(one_across.lower()) == sorted(three_down.lower())
    
    return {
        'words': {'across': across, 'down': down},
        'word_validity': validity,
        'all_valid': all(validity.values()),
        'anagram_check': {
            '1-Across': one_across,
            '3-Down': three_down,
            'is_anagram': is_anagram
        },
        'pass': all(validity.values()) and is_anagram
    }
```
</verification_method>

<common_failures>
- Invalid words: one or more entries aren't real words
- Not anagrams: 1-Across and 3-Down don't have the same letters
- Grid inconsistency: intersecting cells don't match
</common_failures>
</eval>

---

<eval id="VH-004">
<name>Consistent Character Turnaround</name>
<domain>Visual Consistency / Character Design</domain>
<difficulty>Very Hard</difficulty>
<applies_to>Image Generation Models</applies_to>

<prompt>
Create a character turnaround sheet showing the same character from three angles:
1. Front view (facing viewer)
2. Side view (profile, facing right)
3. Back view (facing away)

The character should be: A tall woman with short curly red hair, wearing a long blue coat with silver buttons, black pants, and brown boots. She has a small scar above her left eyebrow.

All three views must show THE SAME character with consistent:
- Height and body proportions
- Hair style, length, and color
- Clothing details (number of buttons, coat length, boot height)
- Distinguishing features (the scar should be visible in front and side view)

Arrange the three views side by side with clear labels.
</prompt>

<success_criteria>
1. Three distinct views: front, side, back
2. Same character across all views (not three different people)
3. Consistency checklist:
   - Hair: same red color, short, curly in all views
   - Coat: blue, same length, silver buttons visible where appropriate
   - Pants: black in all views
   - Boots: brown, same height in all views
   - Scar: above left eyebrow, visible in front and side views
4. Proportions match across views
5. Views are clearly labeled or arranged
</success_criteria>

<verification_method>
Visual inspection with detailed checklist:
- Count buttons in front view
- Measure relative coat length across views
- Check hair consistency
- Locate scar in applicable views
- Compare body proportions
</verification_method>

<common_failures>
- Hair changes across views
- Different number of buttons
- Coat length varies
- Scar missing from one view
- Color shifts between views
- Proportion drift
</common_failures>
</eval>

---

<eval id="VH-005">
<name>Hands Holding Specific Playing Cards</name>
<domain>Visual Accuracy / Fine Detail</domain>
<difficulty>Very Hard</difficulty>
<applies_to>Image Generation Models</applies_to>

<prompt>
Generate an image of a human hand holding exactly four playing cards, fanned out so all four are partially visible. The cards must be, from left to right:

1. Seven of Hearts (7♥)
2. Jack of Spades (J♠)
3. Three of Diamonds (3♦)
4. Ace of Clubs (A♣)

Requirements:
- Standard playing card appearance
- All four cards identifiable (suit and rank visible)
- The hand has exactly five fingers
- Natural hand position for holding fanned cards
</prompt>

<success_criteria>
1. Hand anatomy: exactly 5 digits with natural proportions
2. Card count: exactly 4 cards
3. Card identification (left to right):
   - Card 1: 7♥
   - Card 2: J♠
   - Card 3: 3♦
   - Card 4: A♣
4. Order matches specification
5. Cards fanned naturally, all identifiable
</success_criteria>

<verification_method>
Visual inspection:
1. Count fingers
2. Count cards
3. Identify each card's suit and rank
4. Verify left-to-right order
</verification_method>

<common_failures>
- Wrong finger count (6 fingers common)
- Wrong card count
- Wrong card identities
- Wrong suits
- Wrong order
- Illegible cards
</common_failures>
</eval>

---

# Tier 3: Hard

---

<eval id="H-001">
<name>Pangram Dialogue</name>
<domain>Constrained Writing</domain>
<difficulty>Hard</difficulty>

<prompt>
Write a conversation between two people—Alex and Jordan—discussing what to have for dinner. The conversation should have exactly 6 lines of dialogue (3 per person, alternating, starting with Alex). Every single line of dialogue must be a perfect pangram (containing every letter of the alphabet at least once). The conversation should flow naturally and make logical sense.
</prompt>

<success_criteria>
1. Exactly 6 lines of dialogue
2. Each line is a perfect pangram (contains a-z)
3. Dialogue alternates between Alex and Jordan
4. Conversation is topically coherent (about dinner)
5. Each line makes sense as a response to the previous
</success_criteria>

<verification_method>
```python
import string

def verify_pangram_dialogue(text):
    import re
    # Extract dialogue lines
    lines = re.findall(r'(?:Alex|Jordan):\s*"?([^"]+)"?', text)
    
    results = []
    for i, line in enumerate(lines):
        missing = [c for c in string.ascii_lowercase if c not in line.lower()]
        results.append({
            'line': i + 1,
            'text': line[:50] + '...' if len(line) > 50 else line,
            'is_pangram': len(missing) == 0,
            'missing': missing
        })
    
    return {
        'line_count': len(lines),
        'results': results,
        'all_pangrams': all(r['is_pangram'] for r in results)
    }
```
Manual assessment for conversational flow.
</verification_method>

<common_failures>
- Near-pangrams missing rare letters (j, q, x, z)
- Forced/unnatural phrasing
- Topic drift to accommodate rare letters
</common_failures>
</eval>

---

<eval id="H-002">
<name>Knot Description and Visualization</name>
<domain>Spatial Reasoning / Verbal-to-Visual</domain>
<difficulty>Hard</difficulty>

<prompt>
Part 1: Describe step-by-step how to tie a bowline knot, assuming the reader has a single rope and no prior knowledge. Be precise about which hand holds what, and the direction of each movement.

Part 2: Now imagine the completed bowline knot lying flat on a table, with the standing end pointing directly away from you and the working end pointing to your right. Describe exactly what shapes and crossings you would see looking down from above. Specify which strand goes over vs under at each crossing.
</prompt>

<success_criteria>
Part 1:
- Instructions are unambiguous and followable
- Following them produces a correct bowline
- Over/under distinctions are correct

Part 2:
- Description matches actual bowline appearance from above
- All crossings correctly identified
- Loop, standing end, and working end positioned correctly
</success_criteria>

<verification_method>
Follow instructions with actual rope, then compare Part 2 description to the result laid flat.
</verification_method>

<common_failures>
- Mirror errors (left/right confusion)
- Over/under reversals
- Vague language without specific direction
</common_failures>
</eval>

---

<eval id="H-003">
<name>Constraint Satisfaction with Backtracking</name>
<domain>Logic / Deduction</domain>
<difficulty>Hard</difficulty>

<prompt>
Five people (A, B, C, D, E) sit in chairs 1-5 (left to right). Determine the arrangement from these clues:

1. B is somewhere to the left of D.
2. A is in chair 1 or chair 5.
3. C is immediately next to E.
4. D is not in chair 5.
5. A is not immediately next to B.
6. E is somewhere to the right of A.

Show your deduction process, including any paths you tried and abandoned.
</prompt>

<success_criteria>
1. Correct final arrangement
2. All 6 clues satisfied
3. Reasoning shows systematic case analysis
4. At least one abandoned branch shown
</success_criteria>

<verification_method>
```python
from itertools import permutations

def check_clues(arrangement):
    # arrangement is tuple like ('A', 'B', 'C', 'D', 'E') for chairs 1-5
    pos = {person: i+1 for i, person in enumerate(arrangement)}
    
    return all([
        pos['B'] < pos['D'],           # Clue 1
        pos['A'] in [1, 5],            # Clue 2
        abs(pos['C'] - pos['E']) == 1, # Clue 3
        pos['D'] != 5,                 # Clue 4
        abs(pos['A'] - pos['B']) != 1, # Clue 5
        pos['E'] > pos['A']            # Clue 6
    ])

solutions = [p for p in permutations('ABCDE') if check_clues(p)]
print(solutions)
```
</verification_method>

<common_failures>
- Premature commitment without backtracking
- Misinterpreting "left of" as "immediately left of"
- Missing valid solutions or accepting invalid ones
</common_failures>
</eval>

---

<eval id="H-004">
<name>Geometric Construction Reasoning</name>
<domain>Geometry / Mathematical Proof</domain>
<difficulty>Hard</difficulty>

<prompt>
Part 1: Describe the step-by-step compass-and-straightedge construction of a regular pentagon inscribed in a given circle.

Part 2: Explain why the same approach would NOT work for constructing a regular heptagon (7 sides). What is mathematically different about 7 compared to 5?
</prompt>

<success_criteria>
Part 1:
- Construction is mathematically valid
- Uses only compass and straightedge operations
- Steps are precise enough to follow
- Result is a regular pentagon

Part 2:
- Correctly states heptagon is NOT constructible
- References Fermat primes or Gauss's theorem
- Explains 5 = 2^(2^1) + 1 is a Fermat prime
- Notes 7 is not a Fermat prime
</success_criteria>

<verification_method>
Compare construction to known valid methods. Verify mathematical reasoning against established theory of constructible polygons.
</verification_method>

<common_failures>
- Vague construction steps
- Using measurement (violates compass-straightedge constraint)
- Failing to identify the specific mathematical obstruction for heptagons
</common_failures>
</eval>

---

<eval id="H-005">
<name>Pronoun Resolution Across Narrative Complexity</name>
<domain>Reading Comprehension / Coreference</domain>
<difficulty>Hard</difficulty>

<prompt>
Read the following passage:

---
Margaret had always admired her grandmother Elena, who had emigrated from Portugal as a young woman. Elena often spoke of her sister Rosa, who had stayed behind. "She was always the brave one," Elena would say, though Margaret privately thought her grandmother was braver for leaving.

One day, Margaret found a letter in Elena's drawer from Rosa, dated 1952. In it, Rosa wrote about her daughter, who had just turned five. "She has your eyes," Rosa wrote to Elena. "Sometimes when she looks at me, I see you looking back."

Elena had never mentioned that Rosa had a daughter. When Margaret asked about it, Elena's eyes filled with tears. "She died young," Elena said quietly. "Tuberculosis. Rosa was never the same after."

Later, Margaret found another letter, from 1965. Rosa's handwriting was shakier. "She would have been eighteen this year," Rosa wrote. "Old enough to have her own adventures." The letter continued: "She always asked about her aunt Elena, even at the end."
---

Question: In Rosa's 1965 letter, who is "she" in "She always asked about her aunt Elena, even at the end"?
</prompt>

<success_criteria>
Correct answer: Rosa's daughter (who died of tuberculosis)

The answer must:
- Identify the correct referent
- Not confuse with Elena, Rosa, or Margaret
- Demonstrate understanding of family relationships
- Note temporal context ("at the end" = before death)
</success_criteria>

<verification_method>
Trace referent chain through the passage. "She would have been eighteen" = the daughter who died. "She always asked" = same referent.
</verification_method>

<common_failures>
- Confusing Rosa with Rosa's daughter
- Losing track across time frames
- Misunderstanding "at the end"
</common_failures>
</eval>

---

<eval id="H-006">
<name>Regex Golf</name>
<domain>Pattern Matching / Formal Languages</domain>
<difficulty>Hard</difficulty>

<prompt>
Write a single regular expression that matches ALL of these strings:

MATCH: cat, cart, cardiac, carrier, carpool

And matches NONE of these:

NO MATCH: car, card, carbon, carol, carpet

Provide the regex and explain why it works.
</prompt>

<success_criteria>
1. Regex matches all 5 MATCH strings
2. Regex rejects all 5 NO MATCH strings
3. Explanation is accurate
</success_criteria>

<verification_method>
```python
import re

def test_regex(pattern):
    must_match = ['cat', 'cart', 'cardiac', 'carrier', 'carpool']
    must_not_match = ['car', 'card', 'carbon', 'carol', 'carpet']
    
    regex = re.compile(f'^{pattern}$')
    
    matches = all(regex.match(s) for s in must_match)
    non_matches = not any(regex.match(s) for s in must_not_match)
    
    return {'matches_correct': matches, 'non_matches_correct': non_matches, 'pass': matches and non_matches}
```
</verification_method>

<common_failures>
- Overfitting (just alternation of all match strings)
- Matching some NO MATCH strings
- Incorrect quantifiers
</common_failures>
</eval>

---

<eval id="H-007">
<name>Accurate Venn Diagram</name>
<domain>Visual Accuracy / Set Theory</domain>
<difficulty>Hard</difficulty>
<applies_to>Image Generation Models</applies_to>

<prompt>
Create a Venn diagram with three overlapping circles labeled A, B, and C. Place these elements in the correct regions:

- Only in A: apple, ant
- Only in B: boat, ball
- Only in C: cat, cake
- In A and B (not C): anchor
- In A and C (not B): acorn  
- In B and C (not A): beach
- In A, B, and C (center): air

The diagram should be clear and readable.
</prompt>

<success_criteria>
1. Three overlapping circles, labeled A, B, C
2. All 10 elements present
3. Each element in the correct region
4. Text is legible
</success_criteria>

<verification_method>
Visual inspection matching each element to its required region.
</verification_method>

<common_failures>
- Elements in wrong regions
- Missing elements
- Illegible text
- Incorrect circle topology
</common_failures>
</eval>

---

<eval id="H-008">
<name>Self-Documenting Function</name>
<domain>Code / Self-Reference</domain>
<difficulty>Hard</difficulty>

<prompt>
Write a Python function called `describe_myself()` that returns a string accurately describing:

1. The number of lines in the function (including the def line)
2. The number of times the letter 'e' appears in the function's source code
3. The names of any built-in functions it uses

The description must be accurate. No file I/O or inspect module allowed.

Example format: "This function has N lines, contains X letter e's, and uses: [list]"
</prompt>

<success_criteria>
1. Function runs without error
2. Line count accurate
3. Letter 'e' count accurate
4. Built-in list complete and accurate
5. No file I/O or inspect module
</success_criteria>

<verification_method>
```python
import inspect

def verify(func):
    source = inspect.getsource(func)
    output = func()
    
    actual_lines = len(source.strip().split('\n'))
    actual_e = source.lower().count('e')
    
    # Parse claimed values from output and compare
    # ... (implementation as before)
```
</verification_method>

<common_failures>
- Bootstrap problem: stating count changes count
- Number words have different 'e' counts
- Incomplete built-in list
</common_failures>
</eval>

---

# Appendix: Quick Reference

| ID | Name | Tier | Key Challenge |
|---|---|---|---|
| EX-001 | Fixed-Point Paragraph | Extreme | Word N has N letters |
| EX-002 | Mutually Describing Sentences | Extreme | Circular constraints |
| EX-003 | Verbal Rubik's Cube | Extreme | 3D state simulation |
| EX-004 | Steganographic Review | Extreme | Hidden acrostic |
| EX-005 | Counting Constraint Cascade | Extreme | 6 simultaneous constraints |
| EX-006 | Pathological Logic Grid | Extreme | Adversarial backtracking |
| EX-007 | Precise Bitmap | Extreme | Shape + exact count |
| EX-008 | Interleaved Dual Narrative | Extreme | Two parallel stories |
| EX-009 | Unbounded Calendar Chain | Extreme | 5+ chained calculations |
| EX-010 | Self-Counting Story | Extreme | Fixed-point with 'a' count |
| VH-001 | Autogram | Very Hard | Self-counting sentence |
| VH-002 | Reverse Quine | Very Hard | Output = reversed source |
| VH-003 | Self-Ref Crossword | Very Hard | Anagram constraint |
| VH-004 | Character Turnaround | Very Hard | Multi-view consistency |
| VH-005 | Cards in Hand | Very Hard | Anatomy + specific cards |
| H-001 | Pangram Dialogue | Hard | Every line is pangram |
| H-002 | Knot Description | Hard | Verbal-to-spatial |
| H-003 | Backtracking Logic | Hard | Must revise deductions |
| H-004 | Geometric Construction | Hard | Math proof required |
| H-005 | Pronoun Resolution | Hard | Complex coreference |
| H-006 | Regex Golf | Hard | Discriminative pattern |
| H-007 | Venn Diagram | Hard | Spatial set membership |
| H-008 | Self-Doc Function | Hard | Code self-reference |

---

# Usage Notes

## What Makes These Different

These evals specifically target capabilities where:
1. **You can't verify until you're done** (the act of generating changes what's correct)
2. **Pattern matching from training fails** (requires actual computation)
3. **Early plausible moves are wrong** (requires backtracking)
4. **Multiple constraints interact** (can't satisfy independently)

## Recommended Testing Protocol

1. **No retries**: First response only. These test planning, not iteration.
2. **Full response**: Don't allow "let me try again." The point is whether the model can get it right without feedback.
3. **Record failure mode**: Not just pass/fail, but *why* it failed.
4. **Version prompts**: Small wording changes can dramatically affect results.

## Difficulty Recalibration

After testing on multiple models, recategorize:
- If >30% of frontier models pass an "Extreme" task, downgrade it
- If <5% pass a "Hard" task, upgrade it

The goal is to maintain tasks at the bleeding edge—always just beyond reliable capability.
