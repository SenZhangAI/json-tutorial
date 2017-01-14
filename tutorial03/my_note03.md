## tutorial03笔记

escape和unescape是非常容易出bug的地方，很多时候没注意就会忘了加斜杠之类。

程序执行前后细抠细节，比如如下程序，在字符串退出前`c->top=head`，保证后置条件成立，这个常容易忘掉。

``` c
static int lept_parse_string(lept_context* c, lept_value* v) {
//some code...
        switch (ch) {
            case '\"':
                len = c->top - head;
                lept_set_string(v, (const char*)lept_context_pop(c, len), len);
                c->json = p;
                return LEPT_PARSE_OK;
            case '\\':
                switch (*p++) {
                    case '\"' : PUTC(c, '\"'); break;
                    case '\\' : PUTC(c, '\\'); break;
                    case '/' : PUTC(c, '/'); break;
                    case 'b' : PUTC(c, '\b'); break;
                    case 'f' : PUTC(c, '\f'); break;
                    case 'n' : PUTC(c, '\n'); break;
                    case 'r' : PUTC(c, '\r'); break;
                    case 't' : PUTC(c, '\t'); break;
                    default:
                               c->top = head;
                               return LEPT_PARSE_INVALID_STRING_ESCAPE;
                }
                break;
            case '\0':
                c->top = head;
                return LEPT_PARSE_MISS_QUOTATION_MARK;
            default:
                PUTC(c, ch);
        }
    }
}
```
