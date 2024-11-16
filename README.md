# Exercise No-2: 
# Playfair Cipher Program

## Aim:

To implement Playfair Cipher encryption and decryption programs in C.

## Algorithm:

1. ### Create the Key Matrix:
   
   - Take a keyword as input.
   - Remove duplicate letters from the keyword.
   - Fill the key matrix (5x5) with the letters of the keyword.
   - Fill the remaining spaces in the matrix with the rest of the letters of the alphabet (excluding 'J' or merging 'I' and 'J').

3. ### Prepare the Plaintext:
   
   - Take a plaintext message as input.
   - Remove spaces and convert all letters to uppercase.
   - Replace 'J' with 'I' if 'J' is not in the key matrix.
   - Insert filler characters like 'X' between repeated letters to break them up.

5. ### Encryption:
   
   - For each pair of letters in the plaintext:
     - If the two letters are in the same row, replace them with the letters to their immediate right (wrapping around if necessary).
     - If the two letters are in the same column, replace them with the letters immediately below them (wrapping around if necessary).
     - If the letters form a rectangle, replace them with the letters on the same row but at the opposite corners of the rectangle.
   - Display the encrypted message.

7. ### Decryption:
   
   - For each pair of letters in the ciphertext:
     - Reverse the encryption rules to retrieve the original plaintext.

## Encryption Code:

```
#include <stdio.h>
#include <string.h>

#define SIZE 5

void createKeyMatrix(char key[], char keyMatrix[SIZE][SIZE]) {
    int dict[26] = {0};
    int k = 0, len = strlen(key);

    for (int i = 0; i < len; i++) {
        if (key[i] == 'J') key[i] = 'I'; // Replace 'J' with 'I'
        if (dict[key[i] - 'A'] == 0) {
            keyMatrix[k / SIZE][k % SIZE] = key[i];
            dict[key[i] - 'A'] = 1;
            k++;
        }
    }

    for (char ch = 'A'; ch <= 'Z'; ch++) {
        if (ch == 'J') continue; // Skip 'J'
        if (dict[ch - 'A'] == 0) {
            keyMatrix[k / SIZE][k % SIZE] = ch;
            k++;
        }
    }
}

void processText(char* text) {
    for (int i = 0; i < strlen(text); i++) {
        if (text[i] == 'J') text[i] = 'I'; // Replace 'J' with 'I'
    }
}

void findPosition(char ch, char keyMatrix[SIZE][SIZE], int* row, int* col) {
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (keyMatrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

void encryptPlayfairCipher(char* plaintext, char keyMatrix[SIZE][SIZE], char* ciphertext) {
    int row1, col1, row2, col2;
    int len = strlen(plaintext);

    for (int i = 0; i < len; i += 2) {
        findPosition(plaintext[i], keyMatrix, &row1, &col1);
        findPosition(plaintext[i+1], keyMatrix, &row2, &col2);

        if (row1 == row2) {
            ciphertext[i] = keyMatrix[row1][(col1 + 1) % SIZE];
            ciphertext[i+1] = keyMatrix[row2][(col2 + 1) % SIZE];
        } else if (col1 == col2) {
            ciphertext[i] = keyMatrix[(row1 + 1) % SIZE][col1];
            ciphertext[i+1] = keyMatrix[(row2 + 1) % SIZE][col2];
        } else {
            ciphertext[i] = keyMatrix[row1][col2];
            ciphertext[i+1] = keyMatrix[row2][col1];
        }
    }
    ciphertext[len] = '\0';
}

int main() {
    char key[] = "KEYWORD";
    char plaintext[] = "HELLO";
    char keyMatrix[SIZE][SIZE];
    char ciphertext[100];

    createKeyMatrix(key, keyMatrix);
    processText(plaintext);

    // Add padding if necessary
    if (strlen(plaintext) % 2 != 0) {
        strcat(plaintext, "X");
    }

    encryptPlayfairCipher(plaintext, keyMatrix, ciphertext);
    printf("Encrypted Message: %s\n", ciphertext);

    return 0;
}
```

## Decryption Code:

```
#include <stdio.h>
#include <string.h>

#define SIZE 5

void createKeyMatrix(char key[], char keyMatrix[SIZE][SIZE]) {
    int dict[26] = {0};
    int k = 0, len = strlen(key);

    for (int i = 0; i < len; i++) {
        if (key[i] == 'J') key[i] = 'I'; // Replace 'J' with 'I'
        if (dict[key[i] - 'A'] == 0) {
            keyMatrix[k / SIZE][k % SIZE] = key[i];
            dict[key[i] - 'A'] = 1;
            k++;
        }
    }

    for (char ch = 'A'; ch <= 'Z'; ch++) {
        if (ch == 'J') continue; // Skip 'J'
        if (dict[ch - 'A'] == 0) {
            keyMatrix[k / SIZE][k % SIZE] = ch;
            k++;
        }
    }
}

void findPosition(char ch, char keyMatrix[SIZE][SIZE], int* row, int* col) {
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (keyMatrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

void decryptPlayfairCipher(char* ciphertext, char keyMatrix[SIZE][SIZE], char* plaintext) {
    int row1, col1, row2, col2;
    int len = strlen(ciphertext);

    for (int i = 0; i < len; i += 2) {
        findPosition(ciphertext[i], keyMatrix, &row1, &col1);
        findPosition(ciphertext[i+1], keyMatrix, &row2, &col2);

        if (row1 == row2) {
            plaintext[i] = keyMatrix[row1][(col1 - 1 + SIZE) % SIZE];
            plaintext[i+1] = keyMatrix[row2][(col2 - 1 + SIZE) % SIZE];
        } else if (col1 == col2) {
            plaintext[i] = keyMatrix[(row1 - 1 + SIZE) % SIZE][col1];
            plaintext[i+1] = keyMatrix[(row2 - 1 + SIZE) % SIZE][col2];
        } else {
            plaintext[i] = keyMatrix[row1][col2];
            plaintext[i+1] = keyMatrix[row2][col1];
        }
    }
    plaintext[len] = '\0';
}

int main() {
    char key[] = "KEYWORD";
    char ciphertext[] = "KDMNK";
    char keyMatrix[SIZE][SIZE];
    char plaintext[100];

    createKeyMatrix(key, keyMatrix);

    decryptPlayfairCipher(ciphertext, keyMatrix, plaintext);
    printf("Decrypted Message: %s\n", plaintext);

    return 0;
}
```

## Sample Output for Encryption:

![image](https://github.com/user-attachments/assets/9e4a913a-74a6-4774-9da1-506ad5486312)


## Sample Output for Decryption:

![image](https://github.com/user-attachments/assets/6502a6d3-0047-4920-afc2-2dae6f01a510)


#### **Result:**

The Playfair Cipher encryption and decryption programs were successfully implemented in C. 
