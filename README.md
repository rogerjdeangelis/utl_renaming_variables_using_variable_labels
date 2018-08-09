# utl_renaming_variables_using_variable_labels
Renaming variables using variable labels.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Renaming variables using variable labels

    see github
    https://tinyurl.com/yalaq83t
    https://github.com/rogerjdeangelis/utl_renaming_variables_using_variable_labels

    see Stackoverflow
    https://tinyurl.com/y8yo4uhn
    https://stackoverflow.com/questions/51772150/sas-macro-rename-variables-using-their-label-values-as-their-new-variable-name


    INPUT
    =====

     HAVE total obs=5

         Variables in Creation Order

     Variable    Type    Len    Label

     NAME        Char      8    Student
     AGE         Num       8    Age_Years
     SEX         Char      1    Gender
     HEIGHT      Num       8    Height_Inches
     WEIGHT      Num       8    Weight_Pounds


      NAME      AGE    SEX    HEIGHT    WEIGHT

     Alfred      14     M      69.0      112.5
     Alice       13     F      56.5       84.0
     Barbara     13     F      65.3       98.0
     Carol       14     F      62.8      102.5
     Henry       14     M      63.5      102.5

     EXAMPLE OUTPUT (LOG dataset)
     ----------------------------

     WORK.LOG total obs=1

     RC_META    RC_RENAME         STATUS

        0           0        Rename Completed   ** meta data and rename sucessful

     WORK.HAVE total obs=5

          ------ Rules Columns Renamed using labels ----

                 AGE_              HEIGHT_    WEIGHT_
     STUDENT    YEARS    GENDER     INCHES     POUNDS

     Alfred       14       M         69.0      112.5
     Alice        13       F         56.5       84.0
     Barbara      13       F         65.3       98.0
     Carol        14       F         62.8      102.5
     Henry        14       M         63.5      102.5

            Variables in Creation Order

     Variable         Type    Len    Label

     STUDENT          Char      8    Student
     AGE_YEARS        Num       8    Age_Years
     GENDER           Char      1    Gender
     HEIGHT_INCHES    Num       8    Height_Inches
     WEIGHT_POUNDS    Num       8    Weight_Pounds


    PROCESS
    =======

    %symdel rc rens / nowarn;
    data log;

      * sas dictionaries are too slow;
      * create the meta data rename x=y;
      if _n_=0 then do;
         %let rc=%sysfunc(dosubl(%nrstr(
             proc transpose data=have(obs=1) out=havXpo(drop=col1);
             var _all_;
             run;quit;
             proc sql;
               select cats(_name_,"=",_label_) into :rens separated by " "
               from havXpo
             ;quit;
         )));
      end;

      * apply the meta data;
      rc=dosubl('
         proc datasets lib=work;
           modify have;
           rename
              &rens.
         ;run;quit;
      ');

      rc_meta= &rc;
      rc_rename=rc;

      if sum(of rc:)=0 then status="Rename Completed";
      else status="Rename Failed";
      drop rc;
      output;

      stop;

    run;quit;


    OUTPUT (new names)
    ==================

     WORK.HAVE total obs=5

            Variables in Creation Order

     Variable         Type    Len    Label

     STUDENT          Char      8    Student
     AGE_YEARS        Num       8    Age_Years
     GENDER           Char      1    Gender
     HEIGHT_INCHES    Num       8    Height_Inches
     WEIGHT_POUNDS    Num       8    Weight_Pounds

    *                _              _       _
     _ __ ___   __ _| | _____    __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \  / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/ | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|  \__,_|\__,_|\__\__,_|

    ;
    data have;
      label
         name   = "Student"
         age    = "Age_Years"
         sex    = "Gender"
         height = "Height_Inches"
         weight = "Weight_Pounds"
      ;
      set sashelp.class(obs=5);
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    see process

