# Multi-Cipher Text Encoder

This Python script allows you to encode and decode text using a variety of ciphers. You can apply multiple ciphers sequentially to a given text. The script also generates a template describing the applied ciphers and the final encoded text, which can be useful for puzzles or web novel content.

## Features

*   Supports multiple cipher types.
*   Allows chaining of ciphers.
*   Prompts for parameters for ciphers that require them (e.g., Vigenère key, Caesar shift).
*   Generates a formatted output template with cipher descriptions and the encoded text.
*   Flexible command-line interface for specifying text, ciphers, and number of operations.

## Requirements

*   Python 3.x

## How to Run

The script is run from the command line using `python3 main.py`.

### Command-Line Arguments

*   `-t TEXT, --text TEXT`:
    *   The input text string you want to encode.
    *   If not provided, defaults to: `"Hello, World! 123 This is a test."`
*   `-c CIPHER1 [CIPHER2 ...], --ciphers CIPHER1 [CIPHER2 ...]`:
    *   A space-separated list of cipher keys to apply in the specified order.
    *   **Rule**: All word-based ciphers must appear before any letter-based ciphers in the sequence.
    *   This argument is mutually exclusive with `--random`. You must use either `--ciphers` or `--random`.
*   `-r, --random`:
    *   Randomly selects ciphers to apply.
    *   If `--num-ciphers` is not specified, defaults to selecting 3 ciphers.
    *   The random selection will pick at most one word-based cipher and place it first if chosen.
    *   This argument is mutually exclusive with `--ciphers`.
*   `-n NUM_CIPHERS, --num-ciphers NUM_CIPHERS`:
    *   An integer specifying the number of ciphers to apply.
    *   **With `--random`**: Determines how many random ciphers are selected.
    *   **With `--ciphers`**: Acts as a limiter. If you provide more ciphers in the `-c` list than `NUM_CIPHERS`, only the first `NUM_CIPHERS` from your list will be used. If `NUM_CIPHERS` is greater than the number of ciphers you listed, all listed ciphers will be used.
    *   If not provided when using `--ciphers`, all specified ciphers are used.
    *   If not provided when using `--random`, it defaults to 3.
*   `-h, --help`:
    *   Shows the help message detailing all arguments and available ciphers.

### Available Ciphers

You can get an up-to-date list of available cipher keys by running `python3 main.py --help`. The script will list all choices for the `--ciphers` argument.

Currently, the available ciphers are:

*   **Word-Based Ciphers** (Manipulate the order or content of whole words):
    *   `reverse_word_order`: Splits the string by white space, then joins the tokens in reverse order.
    *   `word_replacement`: Replaces words with peaceful alternatives based on word length (prompts for dictionary if not using defaults).

*   **Letter-Based Ciphers** (Manipulate individual characters or blocks of characters):
    *   `caesar`: Shifts letters by a specified number of positions (prompts for shift value, e.g., 3, -5).
    *   `atbash`: A simple substitution cipher where letters are mapped to their reverse in the alphabet (A->Z, B->Y, etc.).
    *   `vigenere`: Encrypts using a keyword (prompts for the key).
    *   `block_reverse`: Pads text with '#' to make its length a multiple of 3, splits into 3-char blocks, reverses each block, and concatenates.
    *   `reverse_capitalize`: Reverses the entire string and capitalizes the first letter.
    *   `grid_coordinate`: Maps letters to (row,col) coordinates in a user-defined grid (prompts for grid dimensions `a` and `b`).
    *   `reverse_chars_in_words`: Splits by white space, then reverses the characters within each token.
    *   `hex_encode`: Converts every character to its two-digit hexadecimal representation (e.g., 'A B' becomes '412042').

*(Note: The `ascii_replace` cipher is commented out in the source code but can be re-enabled if needed.)*

### Cipher Parameters

Some ciphers require additional parameters. If you select such a cipher, the script will prompt you to enter the necessary information:

*   **Caesar Cipher (`caesar`)**: Prompts for the shift value (integer between -25 and 25).
    *   Example prompt: `Enter the Caesar cipher shift value (integer between -25 and 25). Press Enter for default (15):`
*   **Vigenère Cipher (`vigenere`)**: Prompts for the Vigenère key.
    *   Example prompt: `Enter the Vigenère cipher key (or press Enter to use default: 'BUTTERFLY'):`
*   **Grid Coordinate Cipher (`grid_coordinate`)**: Prompts for grid dimensions `a` (rows) and `b` (columns).
    *   Example prompt: `Enter grid dimensions 'a' and 'b' (e.g., '5 6'). Press Enter for default (5 6):`
*   **Word Replacement Cipher (`word_replacement`)**: This cipher uses a predefined list of peaceful words. No user input is required for parameters during runtime unless customized to do so.

### Examples

1.  **Apply a specific sequence of ciphers:**
    ```bash
    python3 main.py -t "My secret message" -c word_replacement vigenere caesar
    ```
    This will:
    1.  Apply `word_replacement`.
    2.  Then apply `vigenere` (will prompt for key).
    3.  Then apply `caesar` (will prompt for shift).

2.  **Apply random ciphers (default 3):**
    ```bash
    python3 main.py --text "Encode this randomly" --random
    ```

3.  **Apply 5 random ciphers:**
    ```bash
    python3 main.py -t "Another random test" -r -n 5
    ```

4.  **Specify ciphers but limit to 2:**
    ```bash
    python3 main.py -t "Limit these" -c hex_encode atbash caesar -n 2
    ```
    This will only apply `hex_encode` and then `atbash`.

5.  **Using a word-based cipher followed by letter-based ciphers:**
    ```bash
    python3 main.py -t "Word first" -c reverse_word_order caesar atbash
    ```

6.  **Using multiple word-based ciphers, then letter-based:**
    ```bash
    python3 main.py -t "Many words then letters" -c reverse_word_order word_replacement vigenere
    ```

### Output

The script will output:
1.  The selected ciphers and their types (Word-based/Letter-based).
2.  The original text.
3.  Step-by-step application of each cipher, showing its description and the result after application.
4.  The final encoded text.
5.  A "Cipher Template Output" formatted for use in a web novel or similar context. This template includes:
    *   The final encoded text.
    *   Step-by-step instructions (in decoding order) on how to restore the original plan, using the descriptions of the applied ciphers.

## Understanding the Output Template

The `CIPHER_TEMPLATE` section in the output is designed to be a set of instructions for decoding the message. The steps are listed in the reverse order of how they were applied during encoding.

Example: If you encoded with `CipherA` -> `CipherB` -> `CipherC`.
The template instructions will be:
1.  Instruction to decode `CipherC`.
2.  Instruction to decode `CipherB`.
3.  Instruction to decode `CipherA`.

This helps a reader (or a character in a story) understand how to decrypt the message.
