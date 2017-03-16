
MinGW比较通用的Makefile
--

    #MinGW 5.1.6  
    .PHONY:clean tidy rebuild  
    SRCDIR      =   src  
    LIBDIR      =   lib  
    DOCSDIR     =   docs  
    BUILDDIR    =   build  
      
    CC      =   C:\MinGW\bin\g++.exe  
    SRCEXT      =   cpp  
    DEPEXT      =   depend  
    RM      =   del /f /q  
    MKDIR       =   mkdir  
    RMDIR       =   rmdir /q /s  
    FLAGS       =   -g -Wall -O3 #-save-temps #-fdump-class-hierarchy #-dump_class_hierarchy  
    LIBPATHS    =   -L"C:\MingW\lib" -L"${LIBDIR}"  
    LIBS        =   #-lopengl32 #-mwindows  
    SRCT        =   *.${SRCEXT}  
    SRCS        +=  ${SRCT}  
    SRCS        +=  */${SRCT}  
    SRCS        +=  */*/${SRCT}  
    SRCS        +=  */*/*/${SRCT}  
    SRCS        +=  */*/*/*/${SRCT}  
    SRCS        +=  */*/*/*/*/${SRCT}  
    SRC     =   ${wildcard ${addprefix ${SRCDIR}/,${SRCS}}}  
    OBJ     =   ${patsubst ${SRCDIR}/%.${SRCEXT},${BUILDDIR}/%.o,${SRC}}  
    DEP     =   ${patsubst ${SRCDIR}/%.${SRCEXT},${BUILDDIR}/%.${DEPEXT},${SRC}}  
    BIN     =   ${BUILDDIR}/${notdir ${CURDIR}.exe}  
      
    build: ${BIN}  
    rebuild: tidy build  
    ${BIN}: ${OBJ}  
        @echo make target: ${BIN}  
        @${CC} ${FLAGS} $^ ${LIBPATHS} ${LIBS} -o $@  
    prepare:  
        @echo prepare folders  
        @if not exist ${BUILDDIR} (${MKDIR} ${BUILDDIR} && @echo make directory: ${BUILDDIR})  
        @if not exist ${DOCSDIR} (${MKDIR} ${DOCSDIR} && @echo make directory: ${DOCSDIR})  
    #   @if not exist ${subst /,\,${dir ${@}}} (${MKDIR} ${subst /,\,${dir ${@}}} && @echo make directory: ${subst /,\,${dir ${@}}})  
        @xcopy ${SRCDIR} ${BUILDDIR} /T /E  
          
    clean:  
        @echo remove build directory and docs directory  
        @if exist ${BUILDDIR} (@${RMDIR} ${BUILDDIR})  
        @if exist ${DOCSDIR} (@${RMDIR} ${DOCSDIR})  
    tidy:  
        @echo tidy dependencies, objects and binaries only  
        ${foreach file,${subst /,\,${OBJ}},@if exist ${file} (${RM} ${file}) &&}@echo on  
        ${foreach file,${subst /,\,${DEP}},@if exist ${file} (${RM} ${file}) &&}@echo on  
      
    sinclude ${DEP}  
    ${BUILDDIR}/%.${DEPEXT}:${SRCDIR}/%.${SRCEXT}  
        @if not exist ${BUILDDIR} (${MKDIR} ${BUILDDIR} && @echo make directory: ${BUILDDIR})  
        @if not exist ${subst /,\,${dir ${@}}} (${MKDIR} ${subst /,\,${dir ${@}}} && @echo make directory: ${subst /,\,${dir ${@}}})  
        @echo make dependency for ${patsubst ${SRCDIR}/%.${SRCEXT},${BUILDDIR}/%.o,${<}}  
        @${CC} -MM -MT ${patsubst ${SRCDIR}/%.${SRCEXT},${BUILDDIR}/%.o,${<}} ${<} > ${@}  
        @echo       @@echo make object: ${patsubst ${SRCDIR}/%.${SRCEXT},${BUILDDIR}/%.o,${<}} >> ${@}  
        @echo       @@$${CC} $${FLAGS} -c -o $$@ $${filter %%.${SRCEXT},$$^^} >> ${@}  
