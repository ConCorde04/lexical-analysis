# lexical-analysis
#include <stdio.h>
#include <string.h>
#include <ctype.h>

typedef enum {
    KEYWORD,
    IDENTIFIER,
    CONSTANT,
    OPERATOR,
    SPECIALCHARACTER,
    COMMENT,
    ERROR,
    WHITESPACE
} TokenType;

typedef struct {
    TokenType type;
    char lexeme[100];
} Token;

int isKeyword(char *word) {
    char keywords[32][10] = {
        "break", "case", "char", "const", "continue", "default", "do",
        "double", "else", "enum", "extern", "float", "for", "goto", "in", // Changed "if" to "in"
        "int", "long", "register", "return", "short", "signed", "sizeof",
        "static", "struct", "switch", "typedef", "union", "unsigned", "void",
        "when" // Added "when"
    };

    for (int i = 0; i < 32; ++i) {
        if (strcmp(keywords[i], word) == 0) {
            return 1; // It's a keyword
        }
    }
    return 0; // It's not a keyword
}

TokenType getTokenType(char *token) {
    if (isalpha(token[0]) || token[0] == '_') {
        return isKeyword(token) ? KEYWORD : IDENTIFIER;
    } else if (isdigit(token[0])) {
        return CONSTANT;
    } else if (strchr("+-*/%<>=&|^!~.?,;{}[]#()", token[0]) != NULL) {
        return OPERATOR;
    } else if (strchr("(){}[],;#", token[0]) != NULL) {
        return SPECIALCHARACTER;
    } else if (strcmp(token, "//") == 0 || strcmp(token, "/*") == 0) {
        return COMMENT;
    } else if (isspace(token[0])) {
        return WHITESPACE;
    } else {
        return ERROR;
    }
}

void tokenize(char *input) {
    char *token = strtok(input, " \t\n()+-*/%<>=&|^!~.?,;{}[]#");

    while (token != NULL) {
        Token currentToken;
        strcpy(currentToken.lexeme, token);
        currentToken.type = getTokenType(token);

        // Replace keywords
        if (currentToken.type == KEYWORD) {
            if (strcmp(currentToken.lexeme, "if") == 0) {
                strcpy(currentToken.lexeme, "when");
            } else if (strcmp(currentToken.lexeme, "return") == 0) {
                strcpy(currentToken.lexeme, "give");
            }else if (strcmp(currentToken.lexeme, "switch") == 0) {
                strcpy(currentToken.lexeme, "pick");
            }else if (strcmp(currentToken.lexeme, "case") == 0) {
                strcpy(currentToken.lexeme, "event");
            }
        }


        switch (currentToken.type) {
            case KEYWORD:
                printf("Keyword: %s\n", currentToken.lexeme);
                break;
            case IDENTIFIER:
                printf("Identifier: %s\n", currentToken.lexeme);
                break;
            case CONSTANT:
                printf("Constant: %s\n", currentToken.lexeme);
                break;
            case OPERATOR:
                printf("Operator: %s\n", currentToken.lexeme);
                break;
            case SPECIALCHARACTER:
                printf("SPECIALCHARACTER: %s\n", currentToken.lexeme);
                break;
            case COMMENT:
                printf("Comment: %s\n", currentToken.lexeme);
                if (strcmp(currentToken.lexeme, "//") == 0) {
                    break;
                }
                while ((token = strtok(NULL, "")) != NULL && strcmp(token, "*/") != 0) {
                    // Skip until multi-line comment ends
                }
                if (token != NULL) {
                    printf("Comment: %s\n", token);
                }
                break;
            case ERROR:
                printf("Error: Invalid token: %s\n", currentToken.lexeme);
                break;
            case WHITESPACE:
                // Skip whitespace
                break;
        }

        token = strtok(NULL, " \t\n()+-*/%<>=&|^!~.?,;{}[]#");
    }
}

int main() {
    char input[1000];
    printf("Enter C code:\n");
    fgets(input, sizeof(input), stdin);
    tokenize(input);
    return 0;
}
