# utl-setting-the-length-of-character-variables-to-the-longest-lengths-before-joining-tables
Setting the length of character variables to the longest length before joining tables

    Setting the length of character variables to the longest length before joining tables

    github
    https://tinyurl.com/y48j58xt
    https://github.com/rogerjdeangelis/utl-setting-the-length-of-character-variables-to-the-longest-lengths-before-joining-tables

    SAS Forum
    https://tinyurl.com/y4o2bpv8
    https://communities.sas.com/t5/SAS-Programming/How-to-ensure-like-variables-have-the-same-length-before/m-p/560184

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;


    * note name is longer in have1 and sex is shoter in have 2;
    * I want to append the two tables and have the result;

    data have1;
      length name $8  sex $1;
      set sashelp.class;
      keep name sex age;
    run;quit;

    data have2;
      length name $7 sex $10 ;
      set sashelp.classfit;
      keep name sex age upper;
    run;quit;

                                                           |
          WORK.HAVE1          |        WORK.HAVE2          |  RULE (MERGE DATASET WITH LONGEST LENGTHS)
    LENGTHS
                              |                            |
     Variable    Type    Len  |  Variable    Type    Len   |  Variable    Type    Len
                              |                            |
     NAME        Char      8  |  NAME        Char      7   |  NAME        Char      8   => longer in have1
     SEX         Char      1  |  SEX         Char     10   |  SEX         Char     10   => longer in have2
     AGE         Num       8  |  AGE         Num       8   |  AGE         Num       8
                              |                            |
                              |  UPPER       Num       8   |  UPPER       Num       8

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    WORK.WANT

    Variable    Type    Len

    NAME        Char      8
    SEX         Char     10
    AGE         Num       8

    UPPER       Num       8

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    data have1;
      length name $8  sex $1;
      set sashelp.class;
      keep name sex age;
    run;quit;

    data have2;
      length name $7 sex $10 ;
      set sashelp.classfit;
      keep name sex age upper;
    run;quit;

    proc datasets lib=work;
      delete want;
    run;quit;

    data want;

      * get meta data;
      if _n_=0 then do; %let rc=%sysfunc(dosubl('
           proc sql;
             create
                 table have12 as
             select
                  *
             from
                 have1(obs=0) /* note 0 obs */
             outer
                 union corr   /* need corr option */
             select
                  *
              from
                 have2(obs=0)
           ;quit;
           '));
      end;

      if _n_=0 then set have12;

      merge have1 have2;

    run;quit;


