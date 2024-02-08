---
title: "Flex/Bison error lines compatible with Visual Studio"
datePublished: Thu Nov 17 2016 13:59:00 GMT+0000 (Coordinated Universal Time)
cuid: clsd6rl1o00060akyfyac85z8
slug: flexbison-error-lines-compatible-with-visual-studio
canonical: https://roderickkennedy.com/dbgdiary/flexbison-error-lines-compatible-with-visual-studio

---

Flex/Bison error lines compatible with Visual Studio
====================================================

17 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Flex and Bison can be built from source, to be found at [github.com/AaronNGray/winflexbison](https://github.com/AaronNGray/winflexbison). To get errors and warnings that you can double-click in Visual Studio, you can modify the code as follows:  
  
In the file bison\\src\\location.c, find the function unsigned location\_print (location loc, FILE \*out) - and replace it with

    unsigned
    location_print (location loc, FILE *out)
    {
      unsigned res = 0;
      int end_col = 0 != loc.end.column ? loc.end.column - 1 : 0;
      res += fprintf (out, "%s",
                      quotearg_n_style (3, escape_quoting_style, loc.start.file));
      if(0 <= loc.start.line||loc.start.file != loc.end.file||0 <= loc.end.line)
          res += fprintf (out, "(");
      if (0 <= loc.start.line)
        {
          res += fprintf (out, "%d", loc.start.line);
          if (0 <= loc.start.column)
            res += fprintf (out, ",%d", loc.start.column);
        }
      if (loc.start.file != loc.end.file)
        {
      // Ignore: Visual Studio can't cope with this case.
        }
      if(0 <= loc.start.line||loc.start.file != loc.end.file||0 <= loc.end.line)
          res += fprintf (out, ")");
      return res;
    }
    

That fixes it for Bison. For Flex, in flex/src/parse.c, change the function line\_pinpoint( str, line ) to

    void line_pinpoint( str, line )
    const char *str;
    int line;
     {
     fprintf( stderr, "%s(%d): %s\n", infilename, line, str );
     }
    

My branch incorporating these changes is [here](https://github.com/rvkennedy/winflexbison).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Flex/Bison error lines compatible with Visual Studio
====================================================

17 Nov

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Flex and Bison can be built from source, to be found at [github.com/AaronNGray/winflexbison](https://github.com/AaronNGray/winflexbison). To get errors and warnings that you can double-click in Visual Studio, you can modify the code as follows:  
  
In the file bison\\src\\location.c, find the function unsigned location\_print (location loc, FILE \*out) - and replace it with

    unsigned
    location_print (location loc, FILE *out)
    {
      unsigned res = 0;
      int end_col = 0 != loc.end.column ? loc.end.column - 1 : 0;
      res += fprintf (out, "%s",
                      quotearg_n_style (3, escape_quoting_style, loc.start.file));
      if(0 <= loc.start.line||loc.start.file != loc.end.file||0 <= loc.end.line)
          res += fprintf (out, "(");
      if (0 <= loc.start.line)
        {
          res += fprintf (out, "%d", loc.start.line);
          if (0 <= loc.start.column)
            res += fprintf (out, ",%d", loc.start.column);
        }
      if (loc.start.file != loc.end.file)
        {
      // Ignore: Visual Studio can't cope with this case.
        }
      if(0 <= loc.start.line||loc.start.file != loc.end.file||0 <= loc.end.line)
          res += fprintf (out, ")");
      return res;
    }
    

That fixes it for Bison. For Flex, in flex/src/parse.c, change the function line\_pinpoint( str, line ) to

    void line_pinpoint( str, line )
    const char *str;
    int line;
     {
     fprintf( stderr, "%s(%d): %s\n", infilename, line, str );
     }
    

My branch incorporating these changes is [here](https://github.com/rvkennedy/winflexbison).

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)