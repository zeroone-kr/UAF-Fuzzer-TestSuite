### mupdf
- Bug type: use-after-free
- CVE ID: [pending](https://bugs.ghostscript.com/show_bug.cgi?id=701018)
- Download:
```
git clone git://git.ghostscript.com/mupdf.git
git checkout 4422de4f6756b7dc19ca915ae6d63f5ada718ae7
git submodule update --init --recursive
make sanitize
```
- Reproduce: muraster $POC
- ASAN:
```
=================================================================
==14391==ERROR: AddressSanitizer: heap-use-after-free on address 0x612000001630 at pc 0x000000549ab7 bp 0x7fffffff7e50 sp 0x7fffffff7e48
READ of size 4 at 0x612000001630 thread T0
    #0 0x549ab6 in push_clip_stack /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:102:11
    #1 0x54945d in fz_clip_path /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:180:2
    #2 0x71b7d0 in fz_test_clip_path /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/test-device.c:400:3
    #3 0x5496f2 in fz_clip_path /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:185:4
    #4 0xa4b731 in pdf_show_path /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-op-run.c:672:4
    #5 0xa39150 in pdf_run_n /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-op-run.c:1581:2
    #6 0x92fd2f in pdf_process_keyword /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-interpret.c:611:31
    #7 0x928bda in pdf_process_stream /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-interpret.c:914:6
    #8 0x9268c8 in pdf_process_contents /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-interpret.c:1002:3
    #9 0x99b264 in pdf_run_page_contents_with_usage /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-run.c:100:3
    #10 0x99a405 in pdf_run_page_contents /home/hongxu/FOT/mupdf/mupdf-asan/source/pdf/pdf-run.c:142:3
    #11 0x554caf in fz_run_page_contents /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/document.c:535:4
    #12 0x5550ad in fz_run_page /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/document.c:610:2
    #13 0x514d45 in drawpage /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1094:5
    #14 0x5103cf in drawrange /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1257:5
    #15 0x50e7a7 in main /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1672:6
    #16 0x7ffff6e24b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310
    #17 0x41d6f9 in _start (/home/hongxu/FOT/mupdf/mupdf-asan/build/sanitize/muraster+0x41d6f9)

0x612000001630 is located 240 bytes inside of 288-byte region [0x612000001540,0x612000001660)
freed by thread T0 here:
    #0 0x4d2b98 in __interceptor_free.localalias.0 (/home/hongxu/FOT/mupdf/mupdf-asan/build/sanitize/muraster+0x4d2b98)
    #1 0x6a34e8 in fz_free_default /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/memory.c:326:2
    #2 0x6a2d47 in fz_free /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/memory.c:289:3
    #3 0x5482dc in fz_drop_device /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:83:3
    #4 0x5149d5 in drawpage /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1071:4
    #5 0x5103cf in drawrange /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1257:5
    #6 0x50e7a7 in main /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1672:6
    #7 0x7ffff6e24b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

previously allocated by thread T0 here:
    #0 0x4d2d50 in malloc (/home/hongxu/FOT/mupdf/mupdf-asan/build/sanitize/muraster+0x4d2d50)
    #1 0x6a3498 in fz_malloc_default /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/memory.c:314:9
    #2 0x6a22fe in do_scavenging_malloc /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/memory.c:23:7
    #3 0x6a2814 in fz_calloc /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/memory.c:174:6
    #4 0x547945 in fz_new_device_of_size /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:9:19
    #5 0x718fde in fz_new_test_device /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/test-device.c:542:24
    #6 0x5147e0 in drawpage /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1060:15
    #7 0x5103cf in drawrange /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1257:5
    #8 0x50e7a7 in main /home/hongxu/FOT/mupdf/mupdf-asan/source/tools/muraster.c:1672:6
    #9 0x7ffff6e24b96 in __libc_start_main /build/glibc-OTsEL5/glibc-2.27/csu/../csu/libc-start.c:310

SUMMARY: AddressSanitizer: heap-use-after-free /home/hongxu/FOT/mupdf/mupdf-asan/source/fitz/device.c:102:11 in push_clip_stack
Shadow bytes around the buggy address:
  0x0c247fff8270: fa fa fa fa fa fa fa fa fd fd fd fd fd fd fd fd
  0x0c247fff8280: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd
  0x0c247fff8290: fd fd fd fd fd fd fd fd fd fd fd fd fa fa fa fa
  0x0c247fff82a0: fa fa fa fa fa fa fa fa fd fd fd fd fd fd fd fd
  0x0c247fff82b0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd
=>0x0c247fff82c0: fd fd fd fd fd fd[fd]fd fd fd fd fd fa fa fa fa
  0x0c247fff82d0: fa fa fa fa fa fa fa fa fd fd fd fd fd fd fd fd
  0x0c247fff82e0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd
  0x0c247fff82f0: fd fd fd fd fd fd fd fd fd fd fd fd fd fd fd fa
  0x0c247fff8300: fa fa fa fa fa fa fa fa 00 00 00 00 00 00 00 00
  0x0c247fff8310: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==14391==ABORTING
```