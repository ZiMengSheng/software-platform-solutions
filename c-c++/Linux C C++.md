# The C Preprocessor

C provides certain language facilities by means of a preprocessor, which is conceptionally a separate first step in compilation.
The two most frequently used features are #include, to include the contents of a file during compilation, and #define, to replace a token by an arbitrary sequence of characters. 

Other features described in this section include conditional compilation and macros with arguments. 

![image-20210326184645473](Linux%20C%20C++.assets/image-20210326184645473.png)

# Executive

.o is typically a non-PIC object file emitted by the compiler (before linker stage) When linked with an exe, the code will be included in the executable -- we bind at link time.
.a is typically an archive library containing one or more .o files [non-PIC]. When linked with an exe, the particular "*.o" files in the archive will be inserted into the executable.
.lo is generally a "library object" that contains PIC code whether manually compiled with gcc -fPIC or using libtool.
.so files are "shared object" files. They contains PIC objects.