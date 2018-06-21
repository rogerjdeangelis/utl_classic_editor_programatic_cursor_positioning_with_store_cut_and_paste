# utl_classic_editor_programatic_cursor_positioning_with_store_cut_and_paste
Classic editor programatic cursor positioning with store cut and paste.  

    Classic editor programatic cursor positioning with store cut and paste

    Somewhat useless but fun but perhaps not always repeatable, depends on editor settings.

    for editor repositories
    https://tinyurl.com/ya6me6yo
    https://github.com/rogerjdeangelis?utf8=%E2%9C%93&tab=repositories&q=classic_editor&type=&language=

    github
    https://tinyurl.com/ybq86py6
    https://github.com/rogerjdeangelis/utl_classic_editor_programatic_cursor_positioning_with_store_cut_and_paste

    This requires the classic editor?
    I don't think the read cursor position command still works (worked in REX/ISPF?)

    PROBLEM
    =======

      FOUR EXAMPLES

        1. Programatically copy a string of text in line 3 column 3 to line 3 column 8 into paste buffer
        2. Programatically copy a box of text from line 5 column 2 to line 6 column 4 into paste buffer
        3. Copy text at ABCD to the end of line into paste buffer
        4. Copy a block of text from one location to another
        5. Copy a block of text starting at 'ABCD' and next line to line 83
        6. IOTA command macro to add a sequence on numbers to the end of program


    INPUT
    =====  ( You need to have the following text in the classic editor with prefix area on

    00001 123456789
    00002
    00003   Roger
    00004
    00005 ABCD
    00006 EFGH

    EXAMPLE OUTPUT

    1.   'Roger' in the paste buffer

    2.   ABCD
         EFGH    in the paste buffer

    3,   The boloc above moved from line 1 col 1 to line 83 col1

    00083 ABCD
    00084 EFGH



    PROCESS
    ========

     1. Programatically copy text from line 3 column 3 to line 3 column 8 into paste buffer

        curpos 3 3;mark;curpos 3 8;store;home;unmark;

        hit ctrl v and 'Roger' will be pasted


     2. Programatically copy a box of text from line 5 column 2 to line 6 column 4

        type 'box 5 2 6 5' on the command line

        * you can put the cursor ops in a macro;
        %macro box/cmd parmbuff;


          %let line1=%scan(&syspbuff,1,%str( ));
          %let col1=%scan(&syspbuff,2,%str( ));

          %let line2=%scan(&syspbuff,3,%str( ));
          %let col2=%scan(&syspbuff,4,%str( ));

          curpos &line1 &col1;mark;curpos &line2 &col2;store;home;unmark;

        %mend box;

        type 'box 5 2 6 5' on the command line

        hit ctrl v and the block below will be pasted

     3. Copy text at ABCD to the end of line into paste buffer

        F ABCD;MARK;HOME;down 1;HOME;MARK;HOME;STORE;HOME;UNMARK;

         hit ctrl v

           ABCD

     4. F ABCD;MARK;HOME;down 2;HOME;MARK;HOME;STORE;HOME;UNMARK;

        cntrl v

        ABCD
        EFGH


     5. F ABCD;MARK;HOME;down 2;HOME;MARK;HOME;STORE;HOME;UNMARK;HOME;83;HOME;PASTE;
        Works but is does not always hit 83

     6. iota 10

        the following will appear at the end of your program

         01
         02
         03
         04
         05
         06
         07
         08
         09
         10

        * put both of these macros in member iota.sas in your autocall library

        %macro iota(utliota1)/cmd
           des="type iota 10 and a list or 01 02 02 - 10 will be added to the editor";
           sub '%iotb';
        %mend iota;


        %macro iotb
        / des="Called by utliota n integers in editor";

        %local ui;
        %let urc=%sysfunc(filename(utliotb1,"work.utliotb1.utliotb1.catams"));

        %put urc=&urc;

        %let ufid = %sysfunc(fopen(&utliotb1,o));

        %do ui = 1 %to &utliota1;

          %let uzdec = %sysfunc(putn(&ui,z%length(&utliota1)..));
          %let urc=%sysfunc(fput(&ufid,&uzdec));
          %let urc=%sysfunc(fwrite(&ufid));
        %end;

        %let urc=%sysfunc(fclose(&ufid));

        %let urc=%sysfunc(filename(utliotb1));

        dm "inc 'work.utliotb1.utliotb1.catams'";

        %mend iotb;


