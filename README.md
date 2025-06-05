# Text Watermarking for PHPBB
## 1. Introduction

This tool is an HTML-based application designed to help writers and content creators deter plagiarism, particularly on platforms like phpBB forums that use BBCode. It applies various obfuscation techniques to text, embedding "invisible" watermarks and altering the structure of the visible text to make automated copying and unauthorized reuse more difficult. The goal is not to make content entirely theft-proof, but to significantly raise the effort required to clean and republish stolen content.

The script takes BBCode as input, processes its text content with watermarking techniques, and outputs modified BBCode that retains the original formatting tags while including the obfuscations.

## 2. Features

* **BBCode Input & Output:** Accepts text formatted with common BBCode tags (e.g., `[b]`, `[i]`, `[color]`) and outputs watermarked text that preserves these original tags.
* **HTML Preview:** Provides an immediate HTML-based preview of how the *text content* (without BBCode rendering) looks with watermarks applied. *Note: Spacing and exact layout in this preview may differ from the final rendering in a phpBB forum.*
* **Customizable Invisible Phrases:** Users can input their own list of phrases to be used for the content of larger invisible watermarks (paragraphs/sentences).
* **LocalStorage Persistence:** Custom watermark phrases are automatically saved in the user's browser local storage (with debouncing for efficiency) and reloaded on subsequent visits.
* **Advanced Obfuscation Techniques:**
    * **Space Character Replacement:** Randomly replaces space characters with a single, difficult-to-see symbol character, styled with a color matching the page background but without altering its font size.
    * **Visible Word Breaking:** Randomly splits visible words into multiple `<span>` elements, each styled with a slightly different near-black color, making it harder to select or parse continuous text.
    * **Invisible Paragraphs/Sentences:**
        * Inserts large blocks of invisible text (sourced from custom or default phrases) at newlines or at the very end of the entire input.
        * These blocks are styled with a very small font size (`[size=1]` in BBCode).
        * The content *within* these invisible blocks is further fragmented into many tiny `<span>` elements, each with a slightly varying color close to the background and a small font size.
    * **Dynamic Coloring:** Both visible (near-black) and invisible (near-background) text colors are slightly randomized to prevent easy detection and removal based on a single static color value.

## 3. How to Use

1.  **Save the Code:** Copy the entire HTML, CSS, and JavaScript code into a single file named `watermarker.html` (or any other `.html` name).
2.  **Open in Browser:** Open this `watermarker.html` file in a modern web browser.
3.  **Input Original Text:** Paste your text, including any BBCode formatting (e.g., `[b]your text[/b]`), into the "Original Text (with BBCode)" textarea.
4.  **Input Custom Watermark Phrases (Optional):**
    * In the "Custom Invisible Paragraph/Sentence Phrases (one per line)" textarea, you can enter your own phrases. Each line will be treated as a separate phrase that can be randomly chosen for invisible paragraphs/sentences.
    * These phrases will be saved to your browser's local storage automatically as you type (with a short delay).
    * If left empty, default phrases from the script's configuration will be used.
5.  **Apply Watermark:** Click the "Apply Watermark" button.
6.  **Review HTML Preview:** The "Watermarked HTML Preview" area will show your text with visible watermarking effects applied to the plain text content. Note that this preview renders HTML directly and may not perfectly reflect the final spacing or appearance in a BBCode forum.
7.  **Use BBCode Output:** The "Watermarked BBCode Output (Forums)" textarea will contain the final, processed BBCode. Copy this and paste it into your phpBB forum post editor.

## 4. Watermarking Techniques Explained

The script employs several layers of obfuscation:

* **Space Replacement:**
    * With a certain probability (`CONFIG.PROB_SPACE_TO_INVISIBLE_CHAR`), space characters are replaced by a single symbol (e.g., '.', '‚', ';', ':', '·') from `CONFIG.MICRO_INVISIBLE_CHARS`.
    * This symbol is wrapped in HTML as `<span class="invisible-space-char" style="color:INVISIBLE_COLOR;">symbol</span>`.
    * In BBCode, this becomes `[color=INVISIBLE_HEX_COLOR]symbol[/color]`. Importantly, no `[size=1]` tag is applied to these specific space replacements, maintaining their original flow size.
* **Visible Word Breaking:**
    * Words longer than `CONFIG.MIN_WORD_LENGTH_TO_BREAK` have a chance (`CONFIG.PROB_BREAK_VISIBLE_WORD`) of being fragmented.
    * If chosen, the word is split into multiple smaller `<span>` segments using the `_generateFragmentedSpansHtml` helper with a "visible" color palette. Each segment gets a slightly different near-black color.
    * Example HTML: `<span style="color:nearBlack1;">Frag</span><span style="color:nearBlack2;">ment</span>`
    * BBCode: `[color=hex1]Frag[/color][color=hex2]ment[/color]`
* **Invisible Paragraphs & Sentences:**
    * **Placement:** These are inserted with a probability (`CONFIG.PROB_NEWLINE_TO_INVISIBLE_PARA`) when a newline character (`\n`) is processed, or at the very end of the entire text input if it doesn't end with a newline (using `CONFIG.PROB_ADD_END_PARA_INVISIBLE_SENTENCE`).
    * **Content:** Uses phrases from the user's custom input or `CONFIG.DEFAULT_INVISIBLE_PARAGRAPH_CONTENT`.
    * **HTML Structure:**
        * An outer container span (e.g., `<span class="invisible-paragraph-container" style="display:block; line-height:0.1px; height:0.1px; overflow:hidden;">...</span>`) makes it take minimal visual space in the HTML preview.
        * The *content* inside this container is processed by `_generateFragmentedSpansHtml` with an "invisible" color palette and a base style of `"font-size:1px;"`. This results in many small child spans: `<span class="invisible-fragment" style="color:invisibleColor1; font-size:1px;">text</span><span class="invisible-fragment" style="color:invisibleColor2; font-size:1px;">...</span>`
    * **BBCode Structure:** The entire block is typically wrapped in `[size=1]...[/size]`. Inside, each fragment becomes `[color=invisibleHex1][size=1]text[/size][/color][color=invisibleHex2][size=1]...[/size][/color]`. (Note: The script attempts to optimize nested `[size=1]` tags.)
* **Dynamic Coloring:**
    * `getVisibleTextColor()` and `getInvisibleTextColor()` functions generate RGB colors that vary slightly from a base (near-black for visible, near-background for invisible). This makes it harder to strip out watermarks by simply removing a single, fixed color. These are converted to HEX for BBCode.
* **HTML to BBCode Conversion:**
    * The script internally generates watermarked text segments as HTML.
    * The `htmlToBbcode` function then parses this HTML (using temporary DOM elements for robustness) and translates the styled `<span>` elements and `<br>` tags into their BBCode equivalents (`[color]`, `[size]`, and `\n`).

## 5. Configuration (via JavaScript `CONFIG` object)

The script's behavior can be fine-tuned by editing the `CONFIG` object at the beginning of the `<script>` tag. Key options include:

* `BACKGROUND_COLOR`, `TEXT_COLOR_BASE`: Base colors for invisible and visible text.
* `COLOR_VARIATION`, `TEXT_COLOR_VARIATION`: Range of randomization for colors.
* `PROB_SPACE_TO_INVISIBLE_CHAR`: Probability of replacing a space with an invisible character.
* `PROB_NEWLINE_TO_INVISIBLE_PARA`: Probability of inserting an invisible paragraph at a newline.
* `PROB_ADD_END_PARA_INVISIBLE_SENTENCE`: Probability of adding an invisible sentence at the very end of the input (if not newline-terminated).
* `PROB_BREAK_VISIBLE_WORD`, `MIN_WORD_LENGTH_TO_BREAK`, `VISIBLE_WORD_BREAK_PROB_PER_CHAR`: Parameters controlling visible word fragmentation.
* `PROB_VISIBLE_SPAN_PREFIX_INVISIBLE`, `PROB_VISIBLE_SPAN_SUFFIX_INVISIBLE`: Probabilities for adding short invisible prefixes/suffixes to words (currently set to 0.0, effectively disabled, to clarify space replacement).
* `DEFAULT_INVISIBLE_PARAGRAPH_CONTENT`: Default phrases used if the custom input textarea is empty.
* `MICRO_INVISIBLE_CHARS`: Pool of characters used for single-character space replacement.
* `LOCALSTORAGE_KEY_CUSTOM_PHRASES`: Key used for storing/retrieving custom phrases.

## 6. Debugging

Within the `<style>` section of the HTML, there are commented-out CSS rules. You can uncomment these rules to make the "invisible" elements visually distinct (e.g., with colored backgrounds or outlines) in the HTML Preview area. This is very helpful for understanding where and how watermarks are being applied.

## 7. Limitations & Considerations

* **HTML Preview Discrepancies:** The HTML preview shows how the *content* is modified but doesn't fully simulate a phpBB rendering engine. Spacing and exact visual layout of the final BBCode post may differ.
* **BBCode Flavor:** The generated BBCode uses common tags (`[color]`, `[size]`). While widely supported, exact rendering can vary slightly between different forum software versions or custom themes.
* **Not Foolproof:** This tool aims to be a deterrent. Determined individuals with technical skills can still attempt to clean the text, though the goal is to make this process tedious and error-prone.
* **Accessibility:** Heavy use of invisible content and text fragmentation can pose challenges for users relying on screen readers or other assistive technologies. Use with consideration.
* **Performance:** Very long texts with high probabilities for fragmentation and insertions might take a noticeable moment to process in the browser.

## 8. Potential Future Improvements

* More sophisticated logic for space character replacement (e.g., using short, unsized alphabetic strings).
* A user interface for adjusting `CONFIG` probabilities without editing the script.
* More advanced BBCode parsing to handle extremely complex or non-standard BBCode structures.

---
