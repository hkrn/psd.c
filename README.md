psd.c
=====

[psd.rb](https://github.com/layervault/psd.rb) like primitive embeddable PSD parser

## install

```sh
$ clib install hkrn/psd.c
```

## usage

```c
#include "psd.h"

#define STB_IMAGE_WRITE_IMPLEMENTATION
#include "stb_image_write.h"

int
main(void)
{
    const psd_header_t *header;
    const psd_uint8_t *pixel_data;
    psd_size_t size;
    psd_uint8_t *bytes;
    psd_buffer_t *buffer;
    psd_document_t *document;
    psd_rsize_t ncomponents, stride;
    FILE *fp;
    long length;
    fp = fopen("import.psd", "r");
    if (fp) {
        fseek(fp, 0, SEEK_END);
        length = ftell(fp);
        fseek(fp, 0, SEEK_SET);
        bytes = (psd_uint8_t *) malloc(length);
        if (bytes) {
             fread(bytes, 1, length, fp);
             fclose(fp);
             buffer = psdBufferCreate(bytes, length);
             document = psdDocumentCreate();
             psdDocumentParse(document, buffer);
             psdDocumentExtractImage(document);
             header = psdDocumentGetHeader(document);
             size = psdHeaderGetSize(header);
             ncomponents = sizeof(psd_uint32_t);
             pixel_data = psdImageGetPixelData(psdDocumentGetImage(document));
             stride = psdHeaderGetStrideLength(header) * ncomponents;
             stbi_write_png("test.png", size.columns, size.rows, ncomponents, pixel_data, stride);
             psdDocumentDestroy(document);
             psdBufferDestroy(buffer);
        }
        free(bytes);
    }
    return 0;
}
```

## license

MIT

