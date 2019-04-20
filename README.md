# utl-extract-a-string-between-two-arbireay-characters
    Extract a string between two arbitrary characters

    I assume there is only one such substring in each record

    Regular expressions are overused in SAS?

    github
    https://tinyurl.com/y4bk2n9c
    https://github.com/rogerjdeangelis/utl-extract-a-string-between-two-arbireay-characters

    SAS Forum
    https://communities.sas.com/t5/New-SAS-User/Extract-specific-pattern/m-p/552529

     SIX SOLUTIONS

       No Regular expressions solutions

        0. Recommended solution Paul Dorfman sashole@bellsouth.net
            str = scan (substrn (str, findc (str, "X")), 1, ",X") ;

        1. Call Scan
            call scan("a"||str,2,pos,len,"X,");
            str=substr(str,pos-1,len);

        2. Index and substring
           str=substr(str,index(str,"X")+1,index(str,",") - index(str,"X") -1);

        3, Input snd scan
           Input @'X' str
           str=scan(str,1,",");

       Regular Expressions solutions

        4. 's/^[^X]*X(.*?)\x2C.*$/$1/'

           str = prxchange('s/^[^X]*X(.*?)\x2C.*$/$1/',1,str);

        5. Multiple prx statements

           prxId + prxParse("/X(\w+?),/i");
           prxMatch(prxId, char_str)
           prxPosn(prxId, 1, char_str);



    There are only a few places where regular expression are superior to
    SAS character functions?

        Problems for regular expressions use SAS character function

         1. Do the two lists have any common words
            if prxmatch('/MAY|STEVE/',"ROGER MAY PENG")>0 then put 'MAY is in both strings';

         2. Distinct Count of 'booboo' in 'boobooboobooboo' should be 2
            cnt=countc(prxchange('s/([booboo]){6}/*/',-1,'boobooboobooboo'),'*') ;

         3. Valid SSN  (SSNs cannot begin with 666 reserved for Satan)

            if prxmatch('/^(?!000)(?!666)(?!9)\d{3}([- ]?)(?!00)\d{2}\1(?!0000)\d{4}$/'
            ,ssn)>0 then put "Valid";

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/
     _ __   ___    _ __  _ ____  __
    | '_ \ / _ \  | '_ \| '__\ \/ /
    | | | | (_) | | |_) | |   >  <
    |_| |_|\___/  | .__/|_|  /_/\_\
                  |_|
    ;

    ************************************************************
    0. Recommended solution Paul Dorfman sashole@bellsouth.net *
    ************************************************************

    data want;
       input str $ 1-20;
       str = scan (substrn (str, findc (str, "X")), 1, ",X") ;
    cards4;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;;;;
    run;quit;

    If X isn't in STR, this returns the whole STR.
    If a blank is more desirable, it can be handled using IFC.
    Regexen are definitely an overkill here.


    **************
    1. Call Scan *
    **************

    data want;
       input str $ 1-20;
       call scan("a"||str,2,pos,len,"X,");
       str=substr(str,pos-1,len);
    cards4;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;;;;
    run;quit;

    ************************
    2. Index and substring *
    ************************

    data want;
       input str $ 1-20;
       str=substr(str,index(str,"X")+1,index(str,",") - index(str,"X") -1);
    cards4;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;;;;
    run;quit;

    *******************
    3, Input snd scan *
    *******************

    data temp;
        length val $ 10;
        informat val $10.;
        input @"X" val;
        put val=;
        val=scan(val,1,",");
    cards4;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;;;;
    run;quit;

    *
     _ __  _ ____  __
    | '_ \| '__\ \/ /
    | |_) | |   >  <
    | .__/|_|  /_/\_\
    |_|
    ;


    *********************************
    4. 's/^[^X]*X(.*?)\x2C.*$/$1/'  *
    *********************************

    shemp
    https://communities.sas.com/t5/user/viewprofilepage/user-id/95354

    data temp;
       length extracted_value $ 10;
       input char_str $ 1-20;
       extracted_value = prxchange('s/^[^X]*X(.*?)\x2C.*$/$1/',1,char_str);
       datalines;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;
    run;quit;


    ***************************
    5. Mutiple prx statements *
    ***************************

    PGStats
    https://communities.sas.com/t5/user/viewprofilepage/user-id/462

    data temp;
    if _n_ = 1 then prxId + prxParse("/X(\w+?),/i");
    length extracted_value $ 10;
    input char_str $ 1-20;
    if prxMatch(prxId, char_str) then
        extracted_value = prxPosn(prxId, 1, char_str);
    drop prxId;
    datalines;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;
    run;quit;

    ****************************
    5. Mutiple prx statements  *
    ****************************

    Ksharp
    https://communities.sas.com/t5/user/viewprofilepage/user-id/18408

    data have;
    input x $40.;
    cards;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;
    run;
    data want;
     set have;
     pid=prxparse('/(?<=x).+?(?=,)/i');
     call prxsubstr(pid,x,p,l);
     want=substr(x,p,l);
    run;


    ****************************
    5. Mutiple prx statements  *
    ****************************

    Ksharp
    https://communities.sas.com/t5/user/viewprofilepage/user-id/18408


    data have;
    input x $40.;
    cards;
    X20l,p5,op3
    cX3z,p4,op2(1)
    ;
    run;
    data want;
     set have;
     pid=prxparse('/(?<=x).+?(?=,)/i');
     call prxsubstr(pid,x,p,l);
     want=substr(x,p,l);
    run;

