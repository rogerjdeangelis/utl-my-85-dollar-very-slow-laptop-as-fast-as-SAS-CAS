# utl-my-85-dollar-very-slow-laptop-as-fast-as-SAS-CAS
My 85 dollar very slow laptop as fast as SAS CAS

    My $85 dollar very slow laptop as fast as SAS CAS

    github
    https://tinyurl.com/vhlymr7
    https://github.com/rogerjdeangelis/utl-my-85-dollar-very-slow-laptop-as-fast-as-SAS-CAS


    https://communities.sas.com/t5/SAS-Communities-Library/CAS-is-Fast/ta-p/628282

    My 85 dollar very slow laptop as fast as SAS CAS

    I don't have my beast workstation, I am on the road with my 16gb 8 core E6420 laptop circa 2007)

    Paul Dorfman

      We have the same laptop, did you know you can update the dell E6420

            1. 16gb ram ($35 does work even though Dell won't admit it))
            2. Docking station with two usb3 ports - $20


    My $85 dollar laptop is almost as fast as CAS

                                    Seconds
    Engine              Method      Real Time

    CAS              Simple.Summary      7.39  -> SAS benchmark
    SAS              PROC MEANS        152.44  -> SAS benchmark

    My laptop                           12.27

    estimate (beast)                     4.00  * estimate (two NVMe drives)

    I bet my $1,000 beast worstation with two nvme drives will take much less than half the time of my laptop
    Will run awhen I am back in DC and have the second NVMe dtive installed (along with best driver)

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    There aare eight facility x product groups. I split and run 8 tasks in parallel

    Table                      Obs

    c:/sd1/m160_11.sas7bdat    20,000,000
    c:/sd1/m160_21.sas7bdat    20,000,000
    d:/sd1/m160_31.sas7bdat    20,000,000
    d:/sd1/m160_41.sas7bdat    20,000,000
    d:/sd1/m160_51.sas7bdat    20,000,000
    h:/sd1/m160_61.sas7bdat    20,000,000
    h:/sd1/m160_71.sas7bdat    20,000,000
    h:/sd1/m160_81.sas7bdat    20,000,000
                              ===========
                              160,000,000


    libname chd "c:/sd1";
    libname dhd "d:/sd1";
    libname hhd "h:/sd1";
    libname out "c:/sd1";

    data chd.m160_1  chd.m160_2 dhd.m160_3 dhd.m160_4 dhd.m160_5 hhd.m160_6 hhd.m160_7 hhd.m160_8;
      length revenue expenses 3.;
      call streaminit(4321);
      do rec=1 to 20000000;
          do facility="A","B";
            do productline="V","W","X","Y";
               grp=cats(facility,productline);
               expenses=int(5*rand('uniform'));
               revenue =int(100*rand('uniform'));
               select (grp);
                 when ("AV") output chd.m160_1;
                 when ("AW") output chd.m160_2;
                 when ("AX") output dhd.m160_3;
                 when ("AY") output dhd.m160_4;
                 when ("BV") output dhd.m160_5;
                 when ("BW") output hhd.m160_6;
                 when ("BX") output hhd.m160_7;
                 when ("BY") output hhd.m160_8;
               end;
             end;
           end;
        end;
        drop grp rec;
        stop;
    run;quit;


    One of the 8
    c:/sd1/m160_11.sas7bdat total obs=20,000,000 (mutiply by 8)

      Obs       FACILITY    PRODUCTLINE  REVENUE    EXPENSES

        1          A             V          37          3
        2          A             V          95          1
        3          A             V          29          0
        4          A             V          11          2
        5          A             V          16          3
        6          A             V          11          1
        7          A             V          21          4
        8          A             V          78          0

    ...

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;


    Up to 40 obs WORK.RESULTS total obs=8

    Obs    FACILITY    PRODUCTLINE    _TYPE_     _FREQ_     SUMREVENUE    SUMEXPENSES

     1        A             V            3      20000000     990236593      39995625
     2        A             W            3      20000000     989774911      40003257
     3        A             X            3      20000000     990098413      39998330
     4        A             Y            3      20000000     990007722      40002526
     5        B             V            3      20000000     990028079      40001253
     6        B             W            3      20000000     989784861      40001790
     7        B             X            3      20000000     989949866      40002720
     8        B             Y            3      20000000     989863982      40000232
                                               160000000

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;




    %let _s=%sysfunc(compbl(C:\Progra~1\SASHome\SASFoundation\9.4\sas.exe -sysin c:\nul
      -sasautos c:\oto -work d:\wrk -rsasuser));

    * this places the macro in my autocall library, c:/oto, so the batch command can find the macro;

    filename ft15f001 "c:\oto\parlel.sas";
    parmcards4;
    %macro parlel(drv,tbl);

       libname ssd "&drv.";

       sasfile ssd.&tbl load;

       proc means data=ssd.&tbl noprint nway;
          var revenue expenses;
          class facility productline;
          output out=ssd.&tbl.1 sum(revenue)=sumRevenue sum(expenses)=sumExpenses;
       run;

       sasfile ssd.&tbl close;

    %mend parlel;
    ;;;;
    run;quit;

    * set up;

    %let tym=%sysfunc(time());

    systask kill sys1 sys2 sys3 sys4  sys5 sys6 sys7 sys8;

    systask command "&_s -termstmt %nrstr(%parlel(c:/sd1,m160_1)) -log d:\log\a1.log" taskname=sys1;
    systask command "&_s -termstmt %nrstr(%parlel(c:/sd1,m160_2)) -log d:\log\a2.log" taskname=sys2;
    systask command "&_s -termstmt %nrstr(%parlel(d:/sd1,m160_3)) -log d:\log\a3.log" taskname=sys3;
    systask command "&_s -termstmt %nrstr(%parlel(d:/sd1,m160_4)) -log d:\log\a4.log" taskname=sys4;
    systask command "&_s -termstmt %nrstr(%parlel(d:/sd1,m160_5)) -log d:\log\a5.log" taskname=sys5;
    systask command "&_s -termstmt %nrstr(%parlel(h:/sd1,m160_6)) -log d:\log\a6.log" taskname=sys6;
    systask command "&_s -termstmt %nrstr(%parlel(h:/sd1,m160_7)) -log d:\log\a7.log" taskname=sys7;
    systask command "&_s -termstmt %nrstr(%parlel(h:/sd1,m160_8)) -log d:\log\a8.log" taskname=sys8;

    waitfor sys1 sys2 sys3 sys4  sys5 sys6 sys7 sys8;
    %put %sysevalf(%sysfunc(time()) - &tym);

    /* 12.3340001105971 */

    * put them back together (0.01 seconds)
    data results;
      set
        'c:/sd1/m160_11.sas7bdat'
        'c:/sd1/m160_21.sas7bdat'
        'd:/sd1/m160_31.sas7bdat'
        'd:/sd1/m160_41.sas7bdat'
        'd:/sd1/m160_51.sas7bdat'
        'h:/sd1/m160_61.sas7bdat'
        'h:/sd1/m160_71.sas7bdat'
        'h:/sd1/m160_81.sas7bdat'
    ;
    run;quit;


    Output
    Up to 40 obs WORK.RESULTS total obs=8

    Obs    FACILITY    PRODUCTLINE    _TYPE_     _FREQ_     SUMREVENUE    SUMEXPENSES

     1        A             V            3      20000000     990236593      39995625
     2        A             W            3      20000000     989774911      40003257
     3        A             X            3      20000000     990098413      39998330
     4        A             Y            3      20000000     990007722      40002526
     5        B             V            3      20000000     990028079      40001253
     6        B             W            3      20000000     989784861      40001790
     7        B             X            3      20000000     989949866      40002720
     8        B             Y            3      20000000     989863982      40000232

    *_
    | | ___   __ _
    | |/ _ \ / _` |
    | | (_) | (_| |
    |_|\___/ \__, |
             |___/
    ;

    NOTE: Additional host information:

     X64_7PRO WIN 6.1.7601 Service Pack 1 Workstation

    NOTE: SAS initialization used:
          real time           0.81 seconds
          cpu time            0.26 seconds

    NOTE: Libref SSD was successfully assigned as follows:
          Engine:        V9
          Physical Name: d:\sd1
    NOTE: The file SSD.M160_3.DATA has been loaded into memory by the SASFILE statement.

    NOTE: There were 20000000 observations read from the data set SSD.M160_3.
    NOTE: The data set SSD.M160_31 has 1 observations and 6 variables.
    NOTE: PROCEDURE MEANS used (Total process time):

          real time           10.83 seconds    *** I expect this to be much faster with 32 cores)
          cpu time            9.79 seconds


    NOTE: The file SSD.M160_3.DATA has been closed by the SASFILE statement.

    NOTE: SAS Institute Inc., SAS Campus Drive, Cary, NC USA 27513-2414
    NOTE: The SAS System used:
          real time           12.11 seconds
          cpu time            10.45 seconds

