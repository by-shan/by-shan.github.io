---
title: "Robert Allison's Wonderful SAS Graphics with GTL"
date: 2017-09-23
categories:
- Notebook
tags:
- SAS
- Visualization
thumbnailImagePosition: left
thumbnailImage: //www.sas.com/content/sascom/en_us/software/platform/open/_jcr_content/par/styledcontainer_795c/par/image_8ae1.img.jpg/1500986649992.jpg
---

This post is copy from Robert Allison's blog, there are 18 wonderful SAS plot ...
<!--more-->




Almost all below are moved from **Robert Allison's blog**. That's great wonderful graphics  and I want to arrange the figures & codes together for my better self-learning. (This is typed by SHAN).
[Robert Allison's Original Blog](http://blogs.sas.com/content/sastraining/2016/12/20/graphs-comparing-r-excel-tableau-spss-matlab-js-python-and-sas/)

## Index
- [Chart1 Likert chart of survey responses](#chart1-likert-chart-of-survey-responses)
- [Chart2 Reading Survery Responses](#chart2-reading-survery-responses) 
- [Chart3 Crime Rate Matrix](#chart3-crime-rate-matrix)
- [Chart4 Series Line Plot](#chart4-series-line-plot)
- [Chart5 Grouped Bar Charts](#chart5-grouped-bar-charts)
- [Chart6 3D Plot](#chart6-3D-plot)
- [Chart7 World Map](#chart7-world-map)
- [Chart8 Scatter Plot](#chart8-scatter-plot)
- [Chart9 Curves](#chart9-curves)
- [Chart10 Nonindependent Random Normal Variables](#chart10-nonindependent-random-normal-variables)
- [Chart11 Iris - Grouped Scatter Plot](#chart11-iris---grouped-scatter-plot)
- [Chart12 Mosquitos in Map](#chart12-mosquitos-in-map)
- [Chart13 Earth in Map](#chart13-earth-in-map)
- [Chart14 Cancer Intervention Research](#chart14-cancer-intervention-research)
- [Chart15 Animation Wealth & Health by Years](#chart15-animation-wealth-&-health-by-years)
- [Chart16 Sales Analysis of Shoe Brands](#chart16-sales-analysis-of-shoe-brands)
- [Chart17 State Population Growth Comparison](#chart17-state-population-growth-comparison)
- [Chart18 Catalyst Experiment](#chart18-catalyst-experiment)


## Chart1 Likert chart of survey responses
![](/img/Robert SAS Graphics/likert.png)

```sas
%let name=likert;
filename odsout '.';

/*
SAS version of chart from:
http://www.datasciencecentral.com/profiles/blogs/visualizations-comparing-tableau-spss-r-excel-matlab
http://api.ning.com/files/LGdhCycw6D*r1dD7bEBBO1j-sC4rHjwsqJlf8VVyZduoiNiWPZdfSpjaYiwqySFlC6Cpt1qB89cDOza3HPWkVr1ruHtwQLQV/untitled1.png
*/

data my_data;
length category $3;
input category bin1 bin2 bin3 bin4 bin5;
sum=bin1+bin2+bin3+bin4+bin5;
datalines;
X1  20 20 20 20 20
X2  18 19 11 30 22
X3  25 20 22 15 18
X4  19 21 10 25 25
X5  23 20 18 17 22
X6  16 15 20 25 24
X7  11 15 25 20 29
X8  12 15 23 24 26
X9   6 25 22 23 24
X10 16 15 20 25 24
X11  8 18 25 29 20
X12 11 20 30 25 14
X13 11 10 32 23 24
X14 17 14 22 25 22
X15 19 19 20 22 20
X16 12 15 20 25 28
X17 16 15 20 25 24
X18 26 15 10 29 20
X19 20 25 10 21 24
X20 36 20 15 15 14
X21 26 25 20 15 14
X22 19 25 20 15 21
X23 16 25 30 15 14
X24 42  9  1 24 24
X25  6 15 30 15 34
X26 10 15 26 29 20
X27  1 30 20 29 20
X28 20 10 20 25 25
run;

data plot_data; set my_data;
length hovertext $300;
hovertext='title='||quote(
 trim(left(category))||'0d'x||
 '------------------------'||'0d'x||
 trim(left(bin1))||' Strongly Disagree'||'0d'x||
 trim(left(bin2))||' Disagree'||'0d'x||
 trim(left(bin3))||' Neutral'||'0d'x||
 trim(left(bin4))||' Agree'||'0d'x||
 trim(left(bin5))||' Strongly Agree'
 );
bin=3; value=bin1*-1; output;
bin=2; value=bin2*-1; output;
bin=1; value=(bin3/2)*-1; output;
bin=4; value=(bin3/2); output;
bin=5; value=bin4; output;
bin=6; value=bin5; output;
run;

proc format;
value bin_fmt
1="Neutral"
2="Disagree"
3="Strongly Disagree"
4=" Neutral"
5="Agree"
6="Strongly Agree"
;
run;

proc format; picture posval low-high='000,009'; run;

goptions device=png;
goptions xpixels=800 ypixels=700;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Likert Survey Chart") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=14pt htext=10pt ctext=gray33;

legend1 label=none order=(3 2 1 5 6) value=(justify=left h=8pt) 
 position=(top center) across=5 shape=bar(.15in,.15in)
 offset=(3,0);

pattern3 v=s c=cxc90220;
pattern2 v=s c=cxf2a07b;
pattern1 v=s c=cxe3e3e3;
pattern4 v=s c=cxe3e3e3;
pattern5 v=s c=cx8ac0db;
pattern6 v=s c=cx006aac;

axis1 label=('Frequency') order=(-80 to 80 by 20) minor=none offset=(0,0);

axis2 label=none offset=(3,3);

title1 ls=1.5 "Likert chart of survey responses";
title2 a=90 h=2 ' ';
title3 a=-90 h=2 ' ';

proc gchart data=plot_data;
format bin bin_fmt.;
format value posval.;
hbar category / discrete
 type=sum sumvar=value nostats descending
 subgroup=bin legend=legend1
 raxis=axis1 maxis=axis2
 space=0 width=1.3
 coutline=grayee
 autoref cref=graydd clipref
 html=hovertext
 des='' name="&name";
run;

title;
proc print data=my_data noobs label;
label bin1='Strongly Disagree';
label bin2='Disagree';
label bin3='Neutral';
label bin4='Agree';
label bin5='Strongly Agree';
var category bin1 bin2 bin3 bin4 bin5 /* sum */;
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart2 Reading Survery Responses
![](/img/Robert SAS Graphics/book_survey.png)

```sas
%let name=book_survey;
filename odsout '.';

/*
SAS version of chart 2 from:
http://www.datasciencecentral.com/profiles/blogs/visualizations-comparing-tableau-spss-r-excel-matlab
http://api.ning.com/files/LGdhCycw6D9wxJqwMI7lHCzJl9-P*yXVhpXUFFEQK5mCbToMiMXDv9517irQPPEiPn7Q2RgB6TUzU8-Vd0H0*DyJ*y-s1HSr/untitled2.png
*/

data my_data;
input question $ 1-80;
question_order+1;
input country $ 1-3 bin1 bin2 bin3 bin4; output;
input country $ 1-3 bin1 bin2 bin3 bin4; output;
input country $ 1-3 bin1 bin2 bin3 bin4; output;
datalines;
I read only if I have to.
USA .20 .30 .30 .20
MEX .24 .35 .31 .10
CAN .26 .35 .20 .19
Reading is one of my favorite hobbies.
USA .30 .40 .20 .10
MEX .20 .32 .36 .12
CAN .28 .34 .22 .16
I like talking about books with other people.
USA .25 .34 .30 .11
MEX .20 .33 .35 .12
CAN .25 .32 .30 .13
I find it hard to finish books.                 
USA .29 .40 .25 .06
MEX .25 .36 .33 .06
CAN .30 .42 .21 .07
I feel happy if I receive a book as a present.  
USA .30 .32 .30 .08
MEX .18 .25 .40 .17
CAN .25 .25 .30 .20
For me, reading is a waste of time.             
USA .35 .39 .16 .10
MEX .44 .44 .08 .04
CAN .38 .38 .14 .10
I enjoy going to the bookstore or a library.    
USA .25 .22 .33 .20
MEX .22 .32 .40 .06
CAN .20 .28 .32 .20
I read only to get information that I need.     
USA .18 .35 .40 .07
MEX .14 .30 .46 .10
CAN .20 .41 .30 .09
I cannot sit still and read for more than a few minutes.
USA .35 .36 .20 .09
MEX .34 .43 .20 .03
CAN .40 .36 .16 .08
I like to express my opinions about books I have read.  
USA .20 .29 .32 .19
MEX .10 .27 .41 .22
CAN .17 .30 .33 .20
I like to exchange books with my friends.               
USA .30 .36 .20 .14
MEX .22 .30 .30 .18
CAN .28 .32 .30 .10
run;

data my_data; set my_data;
if country='USA' then bar_order=1;
if country='MEX' then bar_order=2;
if country='CAN' then bar_order=3;
run;

data plot_data; set my_data;
length hovertext $300;
hovertext='title='||quote(
 trim(left(question))||'0d'x||
 'Country: '||trim(left(country))||'0d'x||
 '------------------------'||'0d'x||
 trim(left(put(bin1,percent7.0)))||' Strongly Disagree'||'0d'x||
 trim(left(put(bin2,percent7.0)))||' Disagree'||'0d'x||
 trim(left(put(bin3,percent7.0)))||' Agree'||'0d'x||
 trim(left(put(bin4,percent7.0)))||' Strongly Agree'
 );
bin=1; value=bin1; output;
bin=2; value=bin2; output;
bin=3; value=bin3; output;
bin=4; value=bin4; output;
run;

proc sql;
create table foo as
select unique question_order as start, question as label
from plot_data;
quit; run;
data control; set foo;
fmtname = 'q_fmt';
type = 'N';
end = START;
run;
proc format lib=work cntlin=control;
run;

proc sql;
create table foo as
select unique bar_order as start, country as label
from plot_data;
quit; run;
data control; set foo;
fmtname = 'bar_fmt';
type = 'N';
end = START;
run;
proc format lib=work cntlin=control;
run;

proc format;
value bin_fmt
1="Strongly Disagree"
2="Disagree"
3="Agree"
4="Strongly Agree"
;
run;

proc sql noprint;
create table anno_questions as
select unique question_order as group, question as text, bar_order as midpoint
from plot_data
where country='USA';
quit; run;

data anno_questions; set anno_questions;
length function $8 color $8 text $300;
hsys='3'; when='a';
position='2'; 
xsys='1'; x=50; ysys='2'; 
function='move'; output;
xsys='7'; ysys='7'; 
x=0; y=1.15; function='move'; output;
function='cntl2txt'; output;
function='label'; size=.; position='5'; color="black"; cbox="white"; 
text=trim(left(group))||'. '||trim(left(text));
output;
run;

data anno_summary; set my_data;
length function $8 color $8;
xsys='1'; ysys='2'; hsys='3'; when='a';
group=question_order;
midpoint=bar_order;
function='label'; position='5'; 
x=2.5;  text=trim(left(put((bin1+bin2)*100,comma8.0))); color="cxce618a"; output;
x=97.5; text=trim(left(put((bin3+bin4)*100,comma8.0))); color="cxac9478"; output;
run;

goptions device=png;
goptions xpixels=700 ypixels=1500;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Reading survey results") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=14pt htext=10pt ctext=gray33;

pattern1 v=s c=cxb03060;
pattern2 v=s c=cxd797af;
pattern3 v=s c=cxc4b9aa;
pattern4 v=s c=cx8b7354;

axis1 style=0 label=none value=(font='albany amt/bold') offset=(1,1);
axis2 style=0 label=none value=none offset=(1,1);
axis3 style=0 label=none order=(0 to 1 by .25) value=(color="gray77")
 major=none minor=none offset=(4,4);

legend1 position=(top center) label=none across=4 value=(justify=left) 
 shape=bar(.15in,.15in) offset=(5,0);

title1 ls=1.5 "Reading Survey Responses:";
proc gchart data=plot_data anno=anno_questions;
format value percent7.0;
format question_order q_fmt.;
format bar_order bar_fmt.;
format bin bin_fmt.;
hbar bar_order / discrete type=sum sumvar=value nostats
 subgroup=bin legend=legend1
 group=question_order
 maxis=axis1 gaxis=axis2 raxis=axis3 noframe
 space=0 gspace=1.3
 coutline=grayee
 refline=.5 cref=gray66 lref=33
 anno=anno_summary
 html=hovertext
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart3 Crime Rate Matrix
![](/img/Robert SAS Graphics/crime_rate.png)

```sas
%let name=crime_rate;
filename odsout '.';

/*
SAS version of chart 2 from:
http://www.datasciencecentral.com/profiles/blogs/visualizations-comparing-tableau-spss-r-excel-matlab
http://api.ning.com/files/LGdhCycw6D-ROjDdxB0qY4RNFaXyVd23HE4J9FtKo-zgb*FMjdCF6vcf1R7QKjc8l16IQf0dBKWt8-gNXRO4aXVMELjBuMCy/untitled3.png
*/

/*
libname here '.';
*/
libname here '\\sashq\root\u\realliso\public_html\democd_compare';

data my_data; set here.crimedata2 (where=(year=99)
 keep = state year 
 ROBBERY_RATE 
 RAPE_RATE 
 MURDER_RATE 
 LARCENY_RATE 
 BURGLARY_RATE 
 MVTHEFT_RATE
 AGG_ASLT_RATE 
 );
run;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Crime Rate graph") style=htmlblue;

ods graphics / width=650px height=620px border=off imagemap=on imagefmt=png imagename="&name";

title1 "U.S. 1990 Crime Rate Matrix by State (sgscatter matrix)";
proc sgscatter data=my_data;
 matrix ROBBERY_RATE RAPE_RATE MURDER_RATE LARCENY_RATE BURGLARY_RATE MVTHEFT_RATE AGG_ASLT_RATE / 
  group=state 
  markerattrs=(symbol=circle size=10px color=cx0000ff)
  transparency=.75
  nolegend
  ;
run;
/*
*/

title;
proc template;
  define statgraph scatterplotmatrix;
    begingraph;
      entrytitle "U.S. 1990 Crime Rate Matrix by State (sgrender scatterplotmatrix)";
      layout gridded / ;
        scatterplotmatrix
          ROBBERY_RATE RAPE_RATE MURDER_RATE LARCENY_RATE BURGLARY_RATE MVTHEFT_RATE AGG_ASLT_RATE 
          /
          group=state 
          markerattrs=(symbol=circle size=10px color=cx0000ff transparency=.75)
          name="matrix"
          ;
      endlayout;
    endgraph;
  end;
run;
proc sgrender data=my_data template=scatterplotmatrix;
run;
/*
*/

title1 "U.S. 1990 Crime Rate Matrix by State (sgscatter compare)";
proc sgscatter data=my_data;
 compare 
  x=(ROBBERY_RATE RAPE_RATE MURDER_RATE LARCENY_RATE BURGLARY_RATE MVTHEFT_RATE AGG_ASLT_RATE)
  y=(ROBBERY_RATE RAPE_RATE MURDER_RATE LARCENY_RATE BURGLARY_RATE MVTHEFT_RATE AGG_ASLT_RATE)
   / 
  group=state 
  markerattrs=(symbol=circle size=10px color=cx0000ff)
  transparency=.75
  ;
run;
/*
*/

proc print data=my_data;
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart4 Series Line Plot
![](/img/Robert SAS Graphics/recession_job_losses.png)

```sas
%let name=recession_job_losses;
filename odsout '.';

/*
SAS version of chart 2 from:
http://www.datasciencecentral.com/profiles/blogs/visualizations-comparing-tableau-spss-r-excel-matlab
http://api.ning.com/files/LGdhCycw6D89g6weijGnsZrCmUmes9sVNTRUqMC5240685Sd9U*7Z5LcEtxhb3kBISvCxmQG72x9d7hAASYwbJB520wLfzld/untitled4.png
*/

/*
Got the data from here:
http://data.bls.gov/timeseries/lns14000000
Selected 1948 to 2015
Clicked 'Go'
Scrolled down & "Download xls"
Save-as
SeriesReport-20161215080537_793823.xlsx
*/

PROC IMPORT OUT=unemp
 DATAFILE="../democd53/SeriesReport-20131210135510.xls"
 DBMS=EXCEL REPLACE;
 RANGE="BLS Data Series$A12:M81";
 GETNAMES=YES;
 MIXED=NO;
 SCANTEXT=YES;
 USEDATE=YES;
 SCANTIME=YES;
RUN;

proc transpose data=unemp (where=(year^=.)) out=unemp;
by year;
run;

data unemp; set unemp;
format date date9.;
date=input('15'||trim(left(_name_))||trim(left(year)),date9.);
format unemployment percent8.2;
unemployment=col1/100;
if date^=. and unemployment^=. then output;
run;


/*
Using dates from:
http://en.wikipedia.org/wiki/List_of_recessions_in_the_United_States
*/
data recessions; 
format start end next date9.;
informat start end next date9.;
input start end next;
datalines;
15nov1948 15oct1949 15jul1953
15jul1953 15may1954 15aug1957
15aug1957 15apr1958 15apr1960
15apr1960 15feb1961 15dec1969
15dec1969 15nov1970 15nov1973
15nov1973 15mar1975 15jan1980
15jan1980 15jul1980 15jul1981
15jul1981 15nov1982 15jul1990
15jul1990 15mar1991 15mar2001
15mar2001 15nov2001 15dec2007
15dec2007 15jun2009 15dec2015
;
run;


/* initialize all_data (create blank data set), so you can append to it later */
data all_data; 
run;


/* Get the data for one recession */
%macro get_info(start, end, next);

/* strategy 2 - assume the first month of the recession was the min unemployment */
proc sql;
select unemployment format comma10.4 into :min 
from unemp
where date=&start;
quit; run;

data foo; set unemp (where=(date>=&start and date<=&next));
format delta percentn7.0;
start=&start;
delta=&min-unemployment;
recession_year=put(&start,year4.);
run;

data foo; set foo;
if _n_=1 then num_months=0;
else num_months+1;
run;

/* Append data for this recession to the all_data data set */
data all_data; set all_data foo;
num_years=num_months/12;
run;
%mend;

/* Loop through all recessions, and get the info for each one */
data _null_;
 set recessions;
   call execute('%get_info('|| start ||','|| end ||','|| next ||');');
   call execute('run;');
run;

/* only keep the good data */
data all_data; set all_data;
if recession_year^=. and num_months^=. then output;
run;

goptions device=png;
goptions xpixels=1000 ypixels=600;
goptions noborder;
 
ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Job Losses & Recovery During Recent U.S. Recessions") style=htmlblue;

goptions gunit=pct htitle=3.7 ftitle="albany amt/bold" htext=2.5 ftext="albany amt/bold" ctext=gray33;

axis1 label=(angle=90 j=c 'Percent Job Loss' j=c font="albany amt" 'Relative to Beginning of Recession')
  order=(-.06 to 0 by .01) major=none minor=none offset=(0,0) value=(t=7 ' ');

axis2 /* label=('Years after start of recession') */ label=none
 style=0 order=(0 to 7 by 1) major=none minor=none offset=(0,0);

footnote1  
 'Years after start of recession    '
 link="http://data.bls.gov/timeseries/lns14000000"
 c=gray font="albany amt"
 ls=1.0 "Data Source: http://data.bls.gov/timeseries/lns14000000";

legend1 label=none position=(top) offset=(4,0);

symbol1  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx3635e0;
symbol2  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx077909;
symbol3  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cxd8701e;
symbol4  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx605076;
symbol5  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cxf2be1a;
symbol6  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx40879e;
symbol7  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cyan;
symbol8  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cxb70c8f;
symbol9  value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx010101;
symbol10 value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cx8d3506;
symbol11 value=circle c=gray h=1.7 interpol=join mode=include width=2 ci=cxfe0202;

title1 ls=1.5 "Percent Job Losses in Post WWII U.S. Recessions";

data all_data; set all_data;
length my_html $200;
my_html='title='||quote(
 'Recession starting in '||lowcase(trim(left(put(start,monyy7.))))||': '||'0d'x
 ||lowcase(put(date,monyy7.))||' unemployment rate = '||trim(left(put(unemployment,percent8.1)))||'0d'x
 ||'which is '||trim(left(put(-1*delta,percentn8.1)))||' worse than'||'0d'x
 ||'at the beginning of recession')
 ||' href="recession_job_losses_info.htm"';
run;

proc gplot data=all_data;
plot delta*num_years=recession_year /
 vaxis=axis1 haxis=axis2 noframe
 autovref cvref=graydd
 autohref chref=graydd
 legend=legend1
 html=my_html
 des='' name="&name";
run;

data recessions; set recessions;
year=year(start);
run;
title1 "Recession Start & End Dates";
title2 "(from " 
 link='https://en.wikipedia.org/wiki/List_of_recessions_in_the_United_States' 
 "Wikipedia)";
footnote;
proc print data=recessions noobs; 
format start end monyy7.;
var year start end;
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart5 Grouped Bar Charts
![](/img/Robert SAS Graphics/cola.png)

```sas
%let name=cola;
filename odsout '.';

/*
SAS version of chart 2 from:
http://www.datasciencecentral.com/profiles/blogs/visualizations-comparing-tableau-spss-r-excel-matlab
http://api.ning.com/files/LGdhCycw6D8EaikyetJUdNHuLH7S9i0AX9zeL5p5NteaEBAhzfZ1zbos-QAFyvc36vHW2av1aeKNxQtZ5tSsdqnh4vPnrvcO/untitled5.png
*/

data my_data;
input Value Age_range $ 5-9 Carrier $ 11-80;
datalines;
0   16-19 AAPT
.17 20-24 AAPT
.14 25-29 AAPT
.28 30-34 AAPT
.10 35-44 AAPT
.11 45-54 AAPT
.11 55-64 AAPT
.04 65+   AAPT
0   16-19 New Tel
0   20-24 New Tel
.09 25-29 New Tel
0   30-34 New Tel
.03 35-44 New Tel
0   45-54 New Tel
0   55-64 New Tel
.04 65+   New Tel
.33 16-19 One-tel
.17 20-24 One-tel
.41 25-29 One-tel
.22 30-34 One-tel
.21 35-44 One-tel
.15 45-54 One-tel
.07 55-64 One-tel
.21 65+   One-tel
1.0 16-19 Optus  
.92 20-24 Optus  
.82 25-29 Optus  
.78 30-34 Optus  
.90 35-44 Optus  
.96 45-54 Optus  
.85 55-64 Optus  
.92 65+   Optus  
0   16-19 Orange 
.33 20-24 Orange 
.50 25-29 Orange 
.56 30-34 Orange 
.56 35-44 Orange 
.44 45-54 Orange 
.56 55-64 Orange 
.21 65+   Orange 
0   16-19 Telstra
.92 20-24 Telstra
.77 25-29 Telstra
.89 30-34 Telstra
.87 35-44 Telstra
.89 45-54 Telstra
.85 55-64 Telstra
.96 65+   Telstra
.67 16-19 Virgin Mobile
.42 20-24 Virgin Mobile
.32 25-29 Virgin Mobile
.28 30-34 Virgin Mobile
.28 35-44 Virgin Mobile
.19 45-54 Virgin Mobile
.11 55-64 Virgin Mobile
.04 65+   Virgin Mobile
.67 16-19 Vodafone      
1.0 20-24 Vodafone      
.95 25-29 Vodafone      
.94 30-34 Vodafone      
.92 35-44 Vodafone      
.70 45-54 Vodafone      
.59 55-64 Vodafone      
.75 65+   Vodafone      
0   16-19 Other         
.08 20-24 Other         
.23 25-29 Other         
.11 30-34 Other         
.08 35-44 Other         
.11 45-54 Other         
.07 55-64 Other         
0   65+   Other         
0   16-19 Don't know    
0   20-24 Don't know    
0   25-29 Don't know    
0   30-34 Don't know    
0   35-44 Don't know    
0   45-54 Don't know    
0   55-64 Don't know    
0   65+   Don't know    
;
run;

data my_data; set my_data;
if value>.5 then inside_pos=(value-.03);
else outside_pos=(value+.05);
run;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Customer OnLine Access (COLA) preference") style=htmlblue;

ods graphics / width=900px height=600px border=off imagemap=on imagefmt=png imagename="&name";

title "Customer OnLine Access (COLA) preference, by Age";
footnote1 c=gray "Unaided awareness by Age: Filter: Male/Fulltime worker; Unweighted; base n=172; 65% filtered out";
footnote2 c=gray "Multiple comparison correction: False Discovery Rate(FDR)(p=0.05)";

proc sgpanel data=my_data noautolegend;
format value percent7.0;
label carrier='00'x;
panelby age_range / layout=columnlattice onepanel colheaderpos=top 
 novarname uniscale=all proportional rows=1;
hbarparm category=carrier response=value / group=age_range dataskin=pressed baselineattrs=(thickness=0);
rowaxis discreteorder=data;
colaxis display=none offsetmin=0;
/* to be able to overlay the 'text' statements, you must use hbarparm rather than hbar */
text y=carrier x=inside_pos text=value  / position=left  strip textattrs=(color=gray55) backlight=.8;
text y=carrier x=outside_pos text=value / position=right strip textattrs=(color=gray55);
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart6 3D Plot
![](/img/Robert SAS Graphics/points_lines_3d.png)

```sas
%let name=points_lines_3d;
filename odsout '.';

/*
SAS isn't really a "3d modeling" software, so please don't set your
expectations too high when it comes to 3d graphs!

trivia: Note that this example was originally shown in the
V6 SAS/Graph examples book (about 30 years ago). I made only
minimal changes to the code, to have it use more modern
smooth-edged (anti-aliased) fonts.
*/

data stardata;
input temp div mass life @@;
datalines;
10 3 10 1  20 22 46 0  30 23 20 9   40 42 16 16  50 33 20 6
10 1 11 2  20 01 44 2  30 21 31 10  40 41 14 12  50 31 21 7
10 4 14 3  20 13 32 4  30 24 34 9   40 43 22 14  50 34 24 2
10 2 09 2  20 12 40 6  30 26 29 8   40 42 20 16  50 26 29 4
10 3 08 3  20 09 33 8  30 24 38 11  40 39 23 18  50 34 38 2
10 2 09 1  20 08 38 1  30 25 47 14  40 38 18 12  50 43 44 1
10 4 10 3  20 15 42 3  30 29 30 14  40 35 22 14  50 39 20 8
10 3 11 2  20 20 36 5  30 28 31 9   40 40 26 15  50 28 31 0
10 2 15 3  20 19 35 7  30 26 25 11  40 39 25 17  50 26 15 4
10 4 12 3  20 14 33 2  30 27 22 8   40 36 23 12  50 27 22 3
10 4 13 3  20 12 37 4  30 26 33 9   40 42 27 14  50 26 33 5
10 2 17 1  20 10 39 6  30 25 43 13  40 40 29 16  50 35 43 7
10 3 14 1  20 08 38 4  30 28 34 8   40 38 28 14  50 28 34 4
10 1 12 1  20 06 41 2  30 26 32 14  40 36 21 12  50 21 22 2
10 1 11 4  20 09 32 2  30 27 31 8   40 39 22 12  50 37 31 2
10 1 20 2  20 11 32 5  30 25 32 16  40 41 22 15  50 35 22 5
10 4 09 2  20 13 39 1  30 28 29 12  40 43 19 15  50 28 29 1
10 3 02 2  20 09 32 5  30 26 32 9   40 39 22 15  50 36 22 5
10 2 05 3  20 07 35 4  30 24 35 15  40 37 25 14  50 24 35 4
10 3 08 1  20 05 38 6  30 23 28 9   40 35 28 16  50 33 28 6
;

proc sort data=stardata;
   by temp descending mass;
run;

proc means data=stardata noprint;
   by temp;
   var div mass life;
   output out=averages mean=xave yave zave
          max=xmax ymax zmax min=xmin ymin zmin;
run;

data plotstar(drop=_type_ _freq_ xmax ymax zmax xmin ymin zmin xave yave zave temp div mass life);
   retain xave yave zave;
   merge averages stardata;
   by temp;
   length function color $8 text $20 style $20;
   xsys='2'; ysys='2'; zsys='2'; when='a';
   text='dot'; position='5'; line=1; style=' ';

   if mass < yave then
      do;
         when='a';
         function='symbol'; x=div; y=mass; z=life;
            size=1.5; color='blue'; output;
         when='b';
      end;
   else
      do;
         when='b';
         function='symbol'; x=div; y=mass; z=life;
            size=1.5; color='blue'; output;
         when='a';
      end;
   function='move'; x=xave; y=yave; z=zave; output;
   function='draw'; x=div; y=mass; z=life;
      size=0.1; color='green'; output;
run;

data setuplab;
   set stardata(keep=temp);
   by temp;
   if first.temp;
run;

data labels;
   xsys='2'; ysys='2'; zsys='2'; hsys='3'; when='a';
   function='label'; position='5'; color='gray99'; style='albany amt'; size=3; line=1;
   merge setuplab averages(where=(temp ne .));
   drop _type_ _freq_ xave yave zave xmax ymax zmax
        xmin ymin zmin texttmp temp;
   length text $20;
   texttmp=trim(left(put(temp,2.)));
   text='  ' || texttmp || ' Deg.';

   x=xave+3.5; y=yave-10; z=zave;
run;

data axislgnd;
   length function color $8 text $20 style $20;
   xsys='3'; ysys='3'; hsys='3'; when='a'; 

   function='label'; line=1; color=''; position='5'; size=3; style=''; 
   text='Divisions per day'; x=73; y=6; output;
   text='Days'; x=4; y=87; output;
   text='Lived'; x=4; y=84; output;
   text='Size in'; x=14; y=13; output;
   text='Angstroms'; x=14; y=10; output;

   text='J'; x=82; y=77+9; color='blue'; size=6; style='special'; position='6'; output;
   text='Observation'; x=87; y=77+9; color=''; size=3; style='albany amt'; output;

   color='green'; line=1; size=.1; 
   function='move'; x=82; y=72+9; output;
   function='draw'; x=84; y=72.5+9; output;
   function='move'; x=82; y=72+9; output;
   function='draw'; x=85; y=72+9; output;
   function='move'; x=82; y=72+9; output;
   function='draw'; x=82; y=70+9; output;
   function='move'; x=82; y=72+9; output;
   function='draw'; x=81; y=70+9; output;
   function='move'; x=82; y=72+9; output;
   function='draw'; x=81; y=72.5+9; output;

   function='label'; text='Average'; x=87; y=72+9; color=''; size=3; output;
run;

data annoall;
   set plotstar labels axislgnd;
run;

data plotdata(keep=div mass life);
   set averages;
   div=xmax; mass=ymax; life=zmax; output;
   div=xmin; mass=ymin; life=zmin; output;
run;

goptions device=png;
goptions noborder;
goptions xpixels=800 ypixels=600;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Points & Lines, in 3d") style=htmlblue;

goptions gunit=pct htitle=4 htext=2.5 ftitle="albany amt/bold" ftext="albany amt" ctext=gray33;

title1 ls=1.25 'Bacterial Growth Rate';
title2 'Streptococcus Thermophilous, by Temperature in Degrees Celsius';

proc g3d data=plotdata;
label div='00'x mass='00'x life='00'x;
scatter mass*div=life / rotate=30 shape='point'
 noneedle color='white'
 xticknum=7 yticknum=7 zticknum=7
 zmax=18 zmin=0 size=1 grid 
 annotate=annoall
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart7 World Map
![](/img/Robert SAS Graphics/earth_at_night.png)

```sas
%let name=earth_at_night;
filename odsout '.';

/* get the city locations */
data dot_anno; set mapsgfk.world_cities;
anno_flag=1;
if idname in ('China' 'Taiwan' 'South Korea' 'Philippines') then do;
 if ranuni(123)<.03 then output;
 end;
else if idname in ('Germany' 'Netherlands' 'Belgium' 'United Kingdom') then do;
 if ranuni(123)<.40 then output;
 end;
else output;
run;

/* get the world map */
data mymap; 
 set mapsgfk.world (where=( (density<=2) and (cont ^= 97) ) drop = x y);
run;

/* project the map and the dots */
data combined; set mymap dot_anno; run;
proc gproject data=combined out=combined dupok project=gall latlong eastlong degrees;
  id id;
run;
data mymap dot_anno; set combined;
  if anno_flag=1 then output dot_anno;
  else output mymap;
run;

data dot_anno; set dot_anno;
xsys='2'; ysys='2'; when='a';
function='point'; color='white'; 
run;

/* put the name of each country in html hover-text */
data hover_data; set mapsgfk.world_attr;
length my_html $100;
my_html='title='||quote(trim(left(idname)));
colorvar=1;
run;

goptions device=png;
goptions xpixels=1450 ypixels=800;
goptions cback=gray55;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm"
 (title="SAS night sky simulation")
 nogtitle style=htmlblue;

title1 f="albany amt/bold" h=14pt "Simulation of Earth's Lights at Night";

pattern1 v=msolid c=black;

proc gmap map=mymap data=hover_data anno=dot_anno;
id id; 
choro colorvar / levels=1 nolegend coutline=gray22  
 html=my_html
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart8 Scatter Plot
![](/img/Robert SAS Graphics/scatter.png)

```sas
%let name=scatter;
filename odsout '.';

data my_data; set sashelp.class;
length my_html $300;
my_html='title='||quote(
 trim(left(name))||'0d'x||
 '--------------'||'0d'x||
 'Height: '||trim(left(height))||' inches'||'0d'x||
 'Weight: '||trim(left(weight))||' pounds'
 );
run;

proc sql;
select avg(height) format=comma8.1 into :a_height separated by ' ' from sashelp.class;
select avg(weight) format=comma8.1 into :a_weight separated by ' ' from sashelp.class;
select count(*) into :n separated by ' ' from sashelp.class;
quit; run;


goptions device=png;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Simple Scatter Plot") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=4.4 htext=2.5 ctext=gray33;

axis1 order=(40 to 80 by 10) minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' j=l 'Height' j=l 'inches');

axis2 order=(0 to 200 by 50) minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' 'Weight (pounds)');

symbol1 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=Aff000066;
symbol2 font='albany amt/unicode' value='25cb'x height=4.5 interpol=none color=A00000066;

title1 ls=1.5 "Scatter plot of SASHELP.CLASS data";

proc gplot data=my_data;
note c=blue move=(9,79) "Avg height = &a_height";
note c=blue move=(9,75) "Avg weight = &a_weight";
note c=blue move=(9,71) "&n students";
plot height*weight=1 height*weight=2 / overlay 
 vaxis=axis1 haxis=axis2 noframe
 autohref chref=graydd
 autovref cvref=graydd
 html=my_html
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart9 Curves
![](/img/Robert SAS Graphics/curves.png)

```sas
%let name=curves;
filename odsout '.';

data my_data;
input x neutral bumpy smooth;
datalines;
1 2.0 3.2 7.2
2 3.3 7.2 6.7
3 6.8 3.1 5.9
4 3.8 6.8 6.3
5 3.5 3.5 6.2
6 2.1 5.9 6.2
7 3.2 3.4 6.1
8 4.6 6.3 5.5
9 1.9 3.7 5.9
10 2.3 6.2 5.8
11 5.8 3.4 4.9
12 4.5 6.2 5.1
13 6.2 2.7 4.7
14 3.0 6.1 5.1
15 5.2 3.0 4.5
16 5.1 5.5 4.4
17 3.9 2.4 4.6
18 6.3 5.9 4.1
19 2.3 3.2 4.7
20 2.4 5.8 4.5
21 4.1 2.2 4.1
22 4.4 4.9 3.8
23 1.8 2.3 4.4
24 4.0 5.1 3.8
25 2.0 1.9 4.1
26 5.9 4.7 3.2
27 6.1 2.3 3.1
28 4.1 5.2 3.4
29 3.2 2.3 3.3
30 1.2 4.5 3.6
31 2.3 2.1 3.5
32 4.7 4.4 2.7
33 3.7 1.9 3.0
34 5.9 4.6 2.5
35 1.6 1.5 3.2
36 6.2 4.1 2.1
37 4.5 2.0 2.2
38 7.2 4.7 1.9
39 4.3 2.1 2.2
40 3.4 4.5 2.15
41 3.1 2.0 2.1
42 4.8 4.0 1.9
43 4.9 1.2 1.6
44 2.7 3.9 1.95
45 1.9 1.8 2.1
46 1.6 4.4 2.1
47 5.5 1.8 1.2
48 2.0 3.8 1.9
49 1.9 1.7 1.9
50 2.2 4.2 1.7
;
run;

data my_data; set my_data;
length my_html $300;
my_html='title='||quote(
 'X = '||trim(left(x))||'0d'x||
 'Bumpy: '||trim(left(bumpy))||'0d'x||
 'Smooth: '||trim(left(smooth))||'0d'x||
 'Neutral: '||trim(left(neutral))
 );
run;


goptions device=png;
goptions xpixels=900 ypixels=400;
goptions border;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Overlay Curves") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' 
 htitle=14pt htext=10pt ctext=gray33;

legend1 position=(bottom center) label=none repeat=2;

symbol1 value=dot height=2.5 interpol=spline width=1 color=cxfabf02;
symbol2 value=dot height=2.5 interpol=spline width=1 color=cxbe4f4d;
symbol3 value=dot height=2.5 interpol=spline width=1 color=cx4d7eba;

axis1 c=gray77 label=none order=(0 to 8 by 1) minor=none offset=(0,0);

axis2 c=gray77 label=none minor=none offset=(0,0);

title1 ls=1.5 "Overlay Curves Plot";

proc gplot data=my_data; 
label smooth='Smooth (r>0)';
label bumpy='Bumpy (r<0)';
label neutral='Neutral (r=0)';
plot bumpy*x=2 smooth*x=3 neutral*x=1 / overlay
 vaxis=axis1 haxis=axis2 cframe=black
 legend=legend1
 autovref cvref=gray44
 html=my_html
 des='' name="&name";
run;


quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart10 Nonindependent Random Normal Variables
![](/img/Robert SAS Graphics/random_scatter.png)

```sas
%let name=random_scatter;
filename odsout '.';

/*
Using example from "SAS 9.3 Functions and CALL Routines: Reference"
https://books.google.com/books?id=GwTewpZva_YC&pg=PA16&lpg=PA16&dq=sas+%22Nonindependent+Random+Normal+Variables%22&source=bl&ots=4wnY8UQASD&sig=ge-vDf9xKWWxmE8sBQB1_FeJg5w&hl=en&sa=X&ved=0ahUKEwiGt-_yxPnQAhWDVywKHabHD4cQ6AEIJzAC#v=onepage&q=sas%20%22Nonindependent%20Random%20Normal%20Variables%22&f=false
*/


data normal;
seed1 = 11111;
seed2 = 22222;
seed3 = 33333;
do i = 1 to 10000;
 call rannor(seed1, x1);
 call rannor(seed2, x2);
 call rannor(seed3, x3);
 output;
 end;
run;


goptions device=png;
goptions xpixels=600 ypixels=600;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="SAS Pseudo-Random Numbers (SAS/Graph gplot)") 
 options(pagebreak='no') style=sasweb;

goptions htitle=15pt htext=11pt ftitle="albany amt/bold" ftext="albany amt" ctext=gray33;

symbol1 font='albany amt/unicode' value='25cf'x height=.70
 interpol=none color=A3174bf55 repeat=3;

axis1 color=graybb value=(color=gray55) label=none minor=none offset=(0,0);

title1 ls=1.5 "Nonindependent Random Normal Variables";
proc gplot data=normal;
plot x1*x3 /* x3*x2 x1*x2 */ / 
 haxis=axis1 vaxis=axis1 
 autohref chref=graydd
 autovref cvref=graydd
 des='' name="&name"; 
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart11 Iris - Grouped Scatter Plot
![](/img/Robert SAS Graphics/iris_flower.png)

```sas
%let name=iris_flower;
filename odsout '.';

data my_data; set sashelp.iris;
label Species='Species:';
length my_html_petal $300;
my_html_petal='title='||quote(
 trim(left(Species))||'0d'x||
 '--------------'||'0d'x||
 'Length: '||trim(left(PetalLength))||' mm'||'0d'x||
 'Width: '||trim(left(PetalWidth))||' mm'
 );
length my_html_sepal $300;
my_html_sepal='title='||quote(
 trim(left(Species))||'0d'x||
 '--------------'||'0d'x||
 'Length: '||trim(left(SepalLength))||' mm'||'0d'x||
 'Width: '||trim(left(SepalWidth))||' mm'
 );
run;

proc sql;
select avg(PetalLength) format=comma8.1 into :a_pl separated by ' ' from sashelp.iris;
select avg(PetalWidth) format=comma8.1 into :a_pw separated by ' ' from sashelp.iris;
select avg(SepalLength) format=comma8.1 into :a_sl separated by ' ' from sashelp.iris;
select avg(SepalWidth) format=comma8.1 into :a_sw separated by ' ' from sashelp.iris;
select count(*) into :n separated by ' ' from sashelp.iris;
quit; run;


goptions device=png;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Iris Flower Data") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=4.4 htext=2.5 ctext=gray33;

symbol1 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=Aff000066;
symbol2 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=A76EE0077;
symbol3 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=A0000ff66;

legend1 position=(top center) label=(font='albany amt/bold') shape=symbol(5,5) repeat=1;

axis1 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' angle=90 'Length (mm)');

axis2 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' 'Width (mm)');

data anno_image;
length function $8;
xsys='1'; ysys='1'; when='a';
function='move'; x=75.5; y=50; output;
function='image'; imgpath='iris_labeled.jpg'; style='fit'; x=100; y=100; output;
run;

title1 ls=1.5 "Iris Flower - Sepal data";
title2 a=90 h=1.5 ' ';
title3 a=-90 h=1.5 ' ';

proc gplot data=my_data;
note c=gray77 move=(69,22) "Avg Sepal Length = &a_sl";
note c=gray77 move=(69,18) "Avg Sepal Width = &a_sw";
note c=gray77 move=(69,14) "&n data observations";
plot SepalLength*SepalWidth=Species / legend=legend1
 vaxis=axis1 haxis=axis2 noframe
 autohref chref=graydd
 autovref cvref=graydd
 anno=anno_image
 html=my_html_sepal
 des='' name="&name";
run;

axis1 order=(0 to 80 by 20) minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' angle=90 'Length (mm)');

axis2 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' 'Width (mm)');

data anno_image;
length function $8;
xsys='1'; ysys='1'; when='a';
function='move'; x=75.5; y=0; output;
function='image'; imgpath='iris_labeled.jpg'; style='fit'; x=100; y=50; output;
run;

title1 ls=1.5 "Iris Flower - Petal data";
title2 a=90 h=1.5 ' ';
title3 a=-90 h=1.5 ' ';

proc gplot data=my_data;
note c=gray77 move=(11,77) "Avg Petal Length = &a_pl";
note c=gray77 move=(11,73) "Avg Petal Width = &a_pw";
note c=gray77 move=(11,69) "&n data observations";
plot PetalLength*PetalWidth=Species / legend=legend1
 vaxis=axis1 haxis=axis2 noframe
 autohref chref=graydd
 autovref cvref=graydd
 anno=anno_image
 html=my_html_petal
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart12 Mosquitos in Map
![](/img/Robert SAS Graphics/west_nile_chicago.png)

```sas
%let name=iris_flower;
filename odsout '.';

data my_data; set sashelp.iris;
label Species='Species:';
length my_html_petal $300;
my_html_petal='title='||quote(
 trim(left(Species))||'0d'x||
 '--------------'||'0d'x||
 'Length: '||trim(left(PetalLength))||' mm'||'0d'x||
 'Width: '||trim(left(PetalWidth))||' mm'
 );
length my_html_sepal $300;
my_html_sepal='title='||quote(
 trim(left(Species))||'0d'x||
 '--------------'||'0d'x||
 'Length: '||trim(left(SepalLength))||' mm'||'0d'x||
 'Width: '||trim(left(SepalWidth))||' mm'
 );
run;

proc sql;
select avg(PetalLength) format=comma8.1 into :a_pl separated by ' ' from sashelp.iris;
select avg(PetalWidth) format=comma8.1 into :a_pw separated by ' ' from sashelp.iris;
select avg(SepalLength) format=comma8.1 into :a_sl separated by ' ' from sashelp.iris;
select avg(SepalWidth) format=comma8.1 into :a_sw separated by ' ' from sashelp.iris;
select count(*) into :n separated by ' ' from sashelp.iris;
quit; run;

goptions device=png;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Iris Flower Data") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=4.4 htext=2.5 ctext=gray33;

symbol1 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=Aff000066;
symbol2 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=A76EE0077;
symbol3 font='albany amt/unicode' value='25cf'x height=4.5 interpol=none color=A0000ff66;

legend1 position=(top center) label=(font='albany amt/bold') shape=symbol(5,5) repeat=1;

axis1 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' angle=90 'Length (mm)');

axis2 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' 'Width (mm)');

data anno_image;
length function $8;
xsys='1'; ysys='1'; when='a';
function='move'; x=75.5; y=50; output;
function='image'; imgpath='iris_labeled.jpg'; style='fit'; x=100; y=100; output;
run;

title1 ls=1.5 "Iris Flower - Sepal data";
title2 a=90 h=1.5 ' ';
title3 a=-90 h=1.5 ' ';

proc gplot data=my_data;
note c=gray77 move=(69,22) "Avg Sepal Length = &a_sl";
note c=gray77 move=(69,18) "Avg Sepal Width = &a_sw";
note c=gray77 move=(69,14) "&n data observations";
plot SepalLength*SepalWidth=Species / legend=legend1
 vaxis=axis1 haxis=axis2 noframe
 autohref chref=graydd
 autovref cvref=graydd
 anno=anno_image
 html=my_html_sepal
 des='' name="&name";
run;

axis1 order=(0 to 80 by 20) minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' angle=90 'Length (mm)');

axis2 minor=none style=0 offset=(0,0)
 label=(font='albany amt/bold' 'Width (mm)');

data anno_image;
length function $8;
xsys='1'; ysys='1'; when='a';
function='move'; x=75.5; y=0; output;
function='image'; imgpath='iris_labeled.jpg'; style='fit'; x=100; y=50; output;
run;

title1 ls=1.5 "Iris Flower - Petal data";
title2 a=90 h=1.5 ' ';
title3 a=-90 h=1.5 ' ';

proc gplot data=my_data;
note c=gray77 move=(11,77) "Avg Petal Length = &a_pl";
note c=gray77 move=(11,73) "Avg Petal Width = &a_pw";
note c=gray77 move=(11,69) "&n data observations";
plot PetalLength*PetalWidth=Species / legend=legend1
 vaxis=axis1 haxis=axis2 noframe
 autohref chref=graydd
 autovref cvref=graydd
 anno=anno_image
 html=my_html_petal
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart13 Earth in Map
![](/img/Robert SAS Graphics/worldquakes_recent.png)

```sas
%let name=worldquakes;
filename odsout '.';

%let minyear=1900;

%let landcolor=cxf1e9e0;
%let outcolor=gray99;

/* For color-binning of magnitude */
%let upper=7.5;
%let lower=4.0;

/* transparent version */
%let color1=A78c92199;   /* weakest/smallest magnitude  */
%let color2=A007fff44;   /*                             */
%let color3=Aff000066;   /* strongest/biggest magnitude */

/* solid version */
%let color1s=cx78c921;   /* weakest/smallest magnitude  (green) */
%let color2s=cx007fff;   /*                             (blue) */
%let color3s=cxff0000;   /* strongest/biggest magnitude (red) */

/* Reduce density, so it's easier to work with */
data whole_map; set maps.world (where=(density<=1 and cont^=97) drop=x y);
 x=long;
 y=lat;
run;

/* Use gproject to clip-and-close polygons for left & right side */
proc gproject data=whole_map out=right_map dupok project=none
  longmin=0 longmax=4;
  id id;
run;
proc gproject data=whole_map out=left_map dupok project=none
  longmin=-4 longmax=0;
  id id;
run;

/*
Move the right half of the map to the left, and make the segment
variables unique, since some segments got split into two.
*/
data right_map; set right_map;
segment=segment+5000;
x=x-(3.14159*2);
run;
data left_map; set left_map;
run;

/* Put map back together. */
data my_map; set right_map left_map;
run;


proc sql;
create table my_mapdata as
select unique id, '1' as colorval
from my_map;
quit; run;


/* 
Go to this page:
http://www.ngdc.noaa.gov/nndc/struts/form?t=101650&s=1&d=1
Scroll to bottom and select
"Return All Selected Events in tab-delimited format for import into Excel"
Click 'Search database'
Then Save-as
worldquakes.txt
*/
PROC IMPORT OUT=quake_data DATAFILE="worldquakes.txt" DBMS=TAB REPLACE;
 GETNAMES=YES;
 DATAROW=2; 
RUN;

data quake_data; set quake_data (where=(year>=&minyear));
datestring=put(month,z2.)||'/'||put(day,z2.)||'/'||trim(left(year));
format date date9.;
date=input(datestring,mmddyy10.);
datestring2=lowcase(put(date,date9.));
/* 
Over the years, magnitudes have been measured in different ways.
Here's the priority list of the order in which I use them.
*/
magnitude=.;
if eq_mag_ms^=. then magnitude=eq_mag_ms;
else
if eq_mag_mw^=. then magnitude=eq_mag_mw;
else
if eq_mag_mb^=. then magnitude=eq_mag_mb;
else
if eq_mag_ml^=. then magnitude=eq_mag_ml;
else
if eq_mag_mfa^=. then magnitude=eq_mag_mfa;

/* Convert eastlong degrees to westlong radians */
x=atan(1)/45 * -1*longitude;
y=atan(1)/45 * latitude;

/* 
move data that was to left of prime meridian (n & s america, etc)
to the right of the map 
*/
if x>0 then x=x-(3.14159*2);
if x^=. and y^=. then output;
run;

/*
proc sort data=quake_data out=quake_data;
 by descending magnitude;
run;
*/

proc sql;
select min(year) into :minyear from quake_data;
select max(year) into :maxyear from quake_data;
select unique datestring2 into :maxdate from quake_data having date=max(date);
quit; run;
%let minyear=%trim(&minyear);
%let maxyear=%trim(&maxyear);


/* Create an annotate dataset, containing pie/circle for each location */
data circle_anno; set quake_data; 
anno_flag=1;
run;

/* combine, project, and separate */
data combined; set my_map circle_anno; run;
proc gproject data=combined out=combined dupok project=cylindri;
  id id;
run;
data my_map circle_anno;
  set combined;
  if anno_flag=1 then output circle_anno;
  else output my_map;
run;


%let max_area=15;
proc sql noprint;
select max(magnitude) into :max_val from circle_anno;
quit; run;

data circle_anno; set circle_anno; 
length function $8 style color $12 text $20 html $1024;
xsys='2'; ysys='2'; hsys='3'; when='a';
if magnitude lt &lower then color="&color1";
else if magnitude lt &upper then color="&color2";
else color="&color3";
function='pie'; style='pempty'; rotate=360; 
size=sqrt((magnitude/&max_val)*&max_area/3.14);
run;

/* Now, add a "fake legend" using annotated stuff ... */
data legend; 
 length function $8 text $20 style $35;
 xsys='3'; ysys='3'; hsys='3'; when='A';
 function='PIE'; line=0; angle=0.0; rotate=360.0; size=1.5; style='SOLID';
 /* add circles ... */
 size=sqrt(((&upper+1)/&max_val)*&max_area/3.14); 
 color="&color3s"; x=3; y=89; style='pempty'; output; 
 size=sqrt((((&upper+&lower)/2)/&max_val)*&max_area/3.14); 
 color="&color2s";  x=3; y=84-.5; style='pempty'; output; 
 size=sqrt(((&lower-1.5)/&max_val)*&max_area/3.14); 
 color="&color1s";  x=3; y=79-.5; style='pempty'; output; 
 /* add text ... */
 function='LABEL'; position='6'; color='dag';
 style='albany amt/bold'; size=2.8;
 text='Magnitude:';  x=2;  y=95; output;
 style='albany amt';  size=2.5;
 text=">= &upper";  x=5; y=89; output;
 text=" < &upper";  x=5; y=84; output;
 text=" < &lower";  x=5; y=79; output;
run;

/* Move the annotated legend to a better location */
data legend; set legend;
 y=y-75;
 x=x+18;
run;


goptions device=png;
goptions xpixels=1200 ypixels=600;
goptions border cback=white;
 
ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Significant Earthquakes") options(pagebreak='no') 
 style=htmlblue;

goptions gunit=pct htitle=4 htext=2.5 ftitle="albany amt" ftext="albany amt" ctitle='black' ctext='black';

pattern1 color=&landcolor v=s;

footnote c=gray j=right link="http://www.ngdc.noaa.gov/nndc/struts/form?t=101650&s=1&d=1"
 "Data from www.ngdc.noaa.gov (as of &maxdate)";

title1 ls=1.5 "Significant Earthquakes";
title2 "Date Range:  year &minyear to &maxdate";
proc gmap map=my_map data=my_mapdata anno=circle_anno;
id id; 
choro colorval / levels=1 coutline=&outcolor  nolegend discrete 
 anno=legend des='' name="&name";
run;

proc sql noprint;
create table summary as
select unique year, count(*) as count
from quake_data
group by year;
quit; run;
data summary; set summary;
length my_html $300;
my_html='title='||quote(trim(left(year))||': '||trim(left(count)));
run;
title1 ls=1.5 "Count of Significant Earthquakes per year";
title2 "Date Range:  year &minyear to &maxdate";
symbol1 v=circle h=1.5 i=needle c=blue;
axis1 minor=none offset=(0,0);
axis2 minor=none order=(&minyear to 2025 by 25) offset=(0,0);
proc gplot data=summary;
plot count*year /
 vaxis=axis1 haxis=axis2 noframe vzero
 autovref cvref=graydd
 html=my_html
 des='' name="&name._graph";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart14 Cancer Intervention Research
![](/img/Robert SAS Graphics/cancer_intervention_cap.png)

```sas
%let name=cancer_intervention;
filename odsout '.';


data my_data;
input condition $ 1-20
  Behavioral Biological Device Drug Genetic Procedure Radiation 
  actual_duration expected_duration;
datalines;
Anal Cancer           . .50 . .75 . .50 . 4.1 3.7
Bladder Cancer        .333 .667 .444 .889 0 .778 .222 4.6 3.7
Brain Cancer          .143 .429 .143 1.00 . .714 .429 2.8 2.4
Breast Cancer         .778 .222 .333 1.00 0 .666 .444 4.8 4.0
Cervical Cancer       .500 .250 .25 .875 . .75 .375 4.6 3.8
Colon Cancer          .778 .444 .333 1 .111 .556 0 3.7 3.1
Gastric Cancer        .556 .333 .555 1 .111 .778 .222 3.9 3.2
Head and Neck Cancer  .556 .333 .111 .889 0 .444 .667 4.7 3.8
Leukemia              .444 .778 .111 .889 .333 .556 0 4.9 4.1
Lung Cancer           .222 .444 .333 1 .111 .778 .667 4.0 3.3
Melanoma              .778 .778 .778 .889 0 .444 .111 4.6 4.3
Myelofibrosis         . . . 1 . .333 . 4.3 3.3
Ovarian Cancer        .556 .778 .222 1 .333 .667 .111
Pancreatic Cancer     .111 .667 .444 .889 .333 .778 .222 3.9 3.2
Prostate Cancer       .444 .556 .222 1 0 .667 .778 4.2 3.8
Rectal Cancer         .375 .125 .25 .875 0 .75 .625 5.0 3.9
Thyroid Cancer        .375 .125 .375 1 . .875 .5 3.8 2.9
;
run;

proc sort data=my_data out=my_data;
by descending condition;
run;
data my_data; set my_data;
condition_number=_n_;
run;

proc transpose 
 data=my_data (drop=actual_duration expected_duration) 
 out=tran_data (rename=(_name_=intervention col1=percentile_of_trials));
by condition condition_number notsorted;
run;
data tran_data; set tran_data;
length id $100;
id=trim(left(condition))||':'||trim(left(intervention));
format percentile_of_trials percent7.1;
run;

/* controls aspect ratio of colored boxes */
%let x_inc=3.8;

data my_map; set my_data;
length id $100;

intervention='Behavioral';
id=trim(left(condition))||':'||trim(left(intervention));
x=1*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Biological';
id=trim(left(condition))||':'||trim(left(intervention));
x=2*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Device';
id=trim(left(condition))||':'||trim(left(intervention));
x=3*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Drug';
id=trim(left(condition))||':'||trim(left(intervention));
x=4*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Genetic';
id=trim(left(condition))||':'||trim(left(intervention));
x=5*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Procedure';
id=trim(left(condition))||':'||trim(left(intervention));
x=6*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

intervention='Radiation';
id=trim(left(condition))||':'||trim(left(intervention));
x=7*&x_inc; y=condition_number; output;
x=x+&x_inc; output;
y=y+1; output;
x=x-&x_inc; output;

run;


proc format;
value ranges
 1 = "N/A"
 2 = "= 0%"
 3 = "<= 20%"
 4 = "<= 40%"
 5 = "<= 60%"
 6 = "<= 80%"
 7 = "> 80%"
;
run;

data tran_data; set tran_data;
format legend_bin ranges.;
if percentile_of_trials=. then legend_bin=1;
else if percentile_of_trials=0 then legend_bin=2;
else if percentile_of_trials<=.20 then legend_bin=3;
else if percentile_of_trials<=.40 then legend_bin=4;
else if percentile_of_trials<=.60 then legend_bin=5;
else if percentile_of_trials<=.80 then legend_bin=6;
else if percentile_of_trials<=1.00 then legend_bin=7;
run;

proc sql;
create table anno_column_headers as
select unique intervention as text, avg(x) as x, max(y)+.7 as y
from my_map
group by intervention;
quit; run;
data anno_column_headers; set anno_column_headers;
xsys='2'; ysys='2'; hsys='3'; when='a';
function='label'; position='5'; 
run;

proc sql;
create table anno_row_headers as
select unique condition as text, min(x)-.25 as x, avg(y) as y
from my_map
group by condition;
quit; run;
data anno_row_headers; set anno_row_headers;
xsys='2'; ysys='2'; hsys='3'; when='a';
function='label'; position='4'; 
run;

data anno_all; 
length text $100;
set anno_column_headers anno_row_headers;
run;


data tran_data; set tran_data;
length my_html $300;
my_html='title='||quote(
 trim(left(condition))||'0d'x||
 trim(left(intervention))||'0d'x||
 trim(left(put(percentile_of_trials,percent7.1)))
 );
run;


goptions device=png;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Cancer Intervention Research") 
 options(pagebreak='no') style=sasweb;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=16pt htext=9pt ctext=gray33;

title1 ls=1.5 "Cancer Intervention Research";
title2 h=5pct ' ';
title3 h=20pct a=90 ' ';
title4 h=15pct a=-90 ' ';

footnote "Proof-of-concept, using made-up data";

pattern1 v=m4n45 c=grayaa;
pattern2 v=s c=white;
pattern3 v=s c=cxfee5d9;
pattern4 v=s c=cxfcae91;
pattern5 v=s c=cxfb6a4a;
pattern6 v=s c=cxde2d26;
pattern7 v=s c=cxa50f15;

legend1 label=(position=top j=c 'Percent of trials' j=c 'using intervention') 
 across=1 position=(middle right) offset=(7,0) mode=share
 value=(justify=left) shape=bar(.15in,.15in) order=descending;

goptions xpixels=900 ypixels=425;
proc gmap data=tran_data map=my_map anno=anno_all;
id id;
choro legend_bin / discrete midpoints=(1 2 3 4 5 6 7)
 legend=legend1
 coutline=grayaa
 html=my_html
 des='' name="&name";
run;

data my_data; set my_data;
length my_html $3200;
my_html='title='||quote(
 trim(left(condition))||'0d'x||
 'Actual duration: '||trim(left(actual_duration))||' years'||'0d'x||
 'Expected duration: '||trim(left(expected_duration))||' years'||'0d'x||
 'difference: '||trim(left(put((actual_duration-expected_duration),comma8.2)))
 );
if condition='Head and Neck Cancer' then condition='Head and Neck\Cancer';
fake_legend='Average Actual\Trial Duration';
run;


data anno_bars; set my_data;
length function $8 color $8;
when='a'; 

xsys='2'; xc=condition; ysys='2'; y=expected_duration; function='move'; output;
xsys='7'; x=-1.9; function='move'; output;
x=0; y=actual_duration; function='draw'; size=0.1; color="blue"; output;
xsys='2'; xc=condition; y=expected_duration; function='move'; output;
xsys='7'; x=-1.9; function='move'; output;
function='cntl2txt'; output;
x=0; y=expected_duration+(actual_duration-expected_duration)/2; 
 function='label'; position='6'; hsys='d'; size=7; color="blue"; 
 text='  '||trim(left(put((actual_duration-expected_duration),comma8.1))); output;

hsys='3';
xsys='2'; xc=condition; y=expected_duration; function='move'; output;
xsys='7'; x=3*-1; function='move'; output;
xsys='7'; x=3*2; function='draw'; size=1.0; color="cxdc5fbd"; output;

run;

data anno_legend_extras;
length function $8 color $8 text $100;
xsys='3'; ysys='3'; hsys='3'; when='a';
function='move'; x=86; y=53; output;
function='draw'; y=y-4; size=0.1; color="blue"; output;
function='move'; x=85; y=42; output;
function='draw'; x=x+2; size=1.0; color="cxdc5fbd"; output;
function='label'; size=.; color=''; position='6';
x=87.5;
y=52; text='difference'; output;
y=42; text='Average Expected'; output;
run;


pattern1 v=s c=cxd7d5c5;

axis1 color=grayaa label=(angle=90 c=gray33 'Trial Duration (in Years)') 
 value=(c=gray33) minor=none offset=(0,0);

axis2 color=grayaa label=none value=(angle=90 c=gray33) split='\' offset=(3,3);

legend1 position=(right middle) mode=share value=('Average Actual')
 label=none shape=bar(.15in,.15in) offset=(5,10);

title1 h=2 ' ';
title2 a=90 h=10 ' ';
title3 a=-90 h=15 ' ';

goptions xpixels=900 ypixels=375;
proc gchart data=my_data anno=anno_bars;
vbar condition / type=sum sumvar=actual_duration
 raxis=axis1 maxis=axis2 noframe
 subgroup=fake_legend legend=legend1 anno=anno_legend_extras
 width=3.0 space=1.7
 coutline=grayaa
 html=my_html
 des='' name="&name._bar";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart15 Animation Wealth & Health by Years
<http://robslink.com/SAS/democd27/gapminder.htm>
![](/img/Robert SAS Graphics/gapminder_cap.png)
code link: <http://robslink.com/SAS/democd27/gapminder_import.sas>
        <http://robslink.com/SAS/democd27/gapminder.sas>
data reference: <http://www.gapminder.org>
```sas
%let name=gapminder;
filename odsout '.';
/***************************************************************
This example uses new 9.4 syntax and capabilities, therefore you
must run it with SAS 9.4 or higher ...
Creating an animation similar to:
http://www.gapminder.org/
Using data from:
http://www.gapminder.org/data/
****************************************************************/
/* sort by population, so the smaller bubbles will be on top/visible */
/*
libname robsdata '\\sashq\root\u\realliso\public_html\democd27\';
*/
libname robsdata '.';
proc sort data=robsdata.gapminder_data (where=(year>=1950)) 
 out=gapminder_data;
 by year descending population;
run;

/* Set some macro variables for bubble size calculations */
proc sql noprint;
select max(population) into :maxpop from gapminder_data;
quit; run;
%let minbub=.3;
%let maxbub=5.5;

/*
I don't want to extend my x-axis larger than $100000,
but some of the values might extend past that, so I create
a special 'truncated' value to plot (and then tweak the
axis values to show '$100,000+').
*/
data gapminder_data; set gapminder_data;
if income_pp>100000 then income_pp_modified=100000;
else income_pp_modified=income_pp;
run;

data anno_stuff; set gapminder_data;
by year;
length function $8 color $20 style $35 text $50;

/* only output certain things once for each graph */
if first.year then do;
 xsys='1'; ysys='1'; hsys='3'; when='b';
 /* white frame */
 x=0; y=0; function='move'; output;
 x=100; y=100; function='bar'; color='white'; style='solid'; output;
 /* big gray year label in background */
 x=50; y=62;
 function='label'; position='5'; color='cxd3e0e6'; size=40;
 style='albany amt/bold';
 text=trim(left(year));
 output;
 /* annotate image of map, as a color legend */
 xsys='1'; ysys='1'; hsys='3'; when='a';
 function='move'; x=56; y=.2; output;
 function='image'; x=100; y=28; imgpath='gapminder_map.png'; style='fit'; output;
 end;

/* create a colored bubble for each country */
xsys='2'; ysys='2'; hsys='3'; when='a';
size=&minbub+((sqrt(population/3.14)/sqrt(&maxpop/3.14))*&maxbub);
x=income_pp_modified; y=life_expectancy;
if region='America' then color='cxe5ff2f';  /* yellow */
/*else if region='Sub-Saharan Africa' then color='cx3f4fff'; */ /* dark blue */
else if region='Sub-Saharan Africa' then color='cxD15FEE'; /* light purple */
else if region='Middle East & North Africa' then color='cx00ff00';  /* bright green */
else if region='South Asia' then color='cx2fbfe5';  /* light blue */
else if region='East Asia & Pacific' then color='cxff2f2f';  /* red */
else if region='Europe & Central Asia' then color='orange'; 
else color='graydd'; 
function='pie'; rotate=360; 
style='psolid'; output;
style='pempty'; color='gray77'; output;

/* label certain bubbles */
if country in ('China' 'India' 'USA' 'Japan' 'South Africa' 'Nigeria'
   'Russia' 'Congo, Dem. Rep.' 'Ethiopia' 'Bangladesh' 'Vietnam'
   'Kuwait' 'Qatar' 'Pakistan' 'Canada' 'Afghanistan' 'Brazil'
   'Mexico' 'Indonesia' 'Cambodia' 'Saudi Arabia' 'Macao, China'
   'Gabon' 'Iran' 'Angola' 'Rwanda' 'Chile' 'Liberia') then do;
 function='label'; text=trim(left(country));
 if text='Congo, Dem. Rep.' then text='Congo';
 if text='Macao, China' then text='Macao';
 position='b'; size=1.8; style='albany amt';
 color='gray33';
 output; 
 end;
run;

options dev=sasprtc printerpath=gif animduration=.4 animloop=1 
 animoverlay=no animate=start center nobyline;

goptions xpixels=800 ypixels=600 cback=cxcedce3 noborder;

ods listing close;
ods html path=odsout body="&name..htm" 
 (title="SAS/Graph version of Gapminder animation") style=htmlblue;
 
goptions gunit=pct htitle=4 htext=2.5 
 ftitle="albany amt/bold" ftext="albany amt"
 ctext=cx526b81;

axis1 color=cx526b81 label=(j=c font='albany amt/bold' angle=90 'Life expectancy (years)') 
 order=(15 to 90 by 5) value=(t=1 '' t=16 '')
 minor=none offset=(0,0);

axis2 color=cx526b81 label=(font='albany bmt/bold' 'Income per person (GDP/capita, PPP$ inflation-adjusted)') 
 logbase=10 logstyle=expand value=(t=4 '$100,000+') offset=(0,0);

symbol1 value=none interpol=none;

title1 ls=1.5 "Wealth & Health of Nations";
title2 a=90 h=1 " ";
title3 a=-90 h=1 " ";

proc gplot data=gapminder_data uniform;
by year;
format life_expectancy comma5.0;
format income_pp_modified dollar8.0;
plot life_expectancy*income_pp_modified=1 /
 anno=anno_stuff
 vaxis=axis1
 haxis=axis2
 autovref cvref=graydd
 chref=graydd
 href=(
  200 300 400 500 600 700 800 900 1000
  2000 3000 4000 5000 6000 7000 8000 9000 10000
  20000 30000 40000 50000 60000 70000 80000 90000 100000
  )
 noframe
 des='' name="&name";
run;

quit;
ods html close;
ods listing;
```
Back to [Index](#index)

## Chart16 Sales Analysis of Shoe Brands
![](/img/Robert SAS Graphics/shoe_sales.png)

```sas
%let name=shoe_sales;
filename odsout '.';

data my_data;
input brand $ 1-20 units_sold average_price;
size_var=units_sold*average_price;
length my_html $300;
my_html='title='||quote(
 trim(left(brand))||'0d'x||
 'Units Sold: '||trim(left(put(units_sold,comma8.0)))||'0d'x||
 'Avg. Price: '||trim(left(put(average_price,dollar8.2)))
 );
datalines;
Reebok               22000 32.00
Lotto                8500 43.00
Woodland             12500 44.00
Puma                 19500 50.00
Adidas               19000 59.50
Fila                 10500 64.00
Nike                 15000 80.00
;
run;

data anno_labels; set my_data;
length test $100;
xsys='2'; ysys='2'; hsys='d'; when='a'; 
x=average_price; y=units_sold;
function='label'; position='5'; size=8;
text=trim(left(brand));
run;

%let gavg=44.00; /* gross average price */

data anno_lines;
length function $8 color $8 text $100;
xsys='2'; ysys='2'; hsys='3'; when='b'; 
x=0; y=15000; function='move'; output;
x=100; y=15000; function='draw'; size=1.0; color='gray77'; output;
x=50; y=0; function='move'; output;
x=50; y=30000; function='draw'; size=1.0; color='gray77'; output;
x=&gavg; y=0; function='move'; output;
x=&gavg; y=30000; function='draw'; size=0.1; line=3; color='blue'; output;
function='label'; size=.;
x=&gavg; y=30000; position='2'; color='blue'; text='Gross Avg.'; output;
xsys='1'; ysys='1'; color='';
x=1; y=0; position='3'; text='Low Price / Low Sales'; output;
x=1; y=100; position='f'; text='Low Price / High Sales'; output;
x=99; y=0; position='1'; text='High Price / Low Sales'; output;
x=99; y=100; position='d'; text='High Price / High Sales'; output;
run;

goptions device=png;
goptions xpixels=700 ypixels=400;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Shoe Sales") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=15pt htext=10pt ctext=gray33;

axis1 label=(a=90 'Units Sold') order=(0 to 30000 by 10000) 
 major=none minor=none offset=(0,0);

axis2 label=('Average Price') order=(0 to 100 by 20) 
 major=none minor=none offset=(0,0);

symbol1 value=dot interpol=none color=blue
 pointlabel=(height=9pt color=gray77 nodropcollisions position=middle justify=center "#brand");

title1 ls=1.5 "Sales Analysis of Shoe Brands" font='albany amt' " (Last Quarter)";
title2 ls=0.8 " ";

proc gplot data=my_data anno=anno_labels;
format units_sold comma8.0;
format average_price dollar5.0;
bubble units_sold*average_price=size_var / 
 bsize=15 bfill=solid bscale=area
 bcolor=A4dc6eccc
 vaxis=axis1 haxix=axis2 noframe
 anno=anno_lines
 autohref chref=gray77 lhref=33
 autovref cvref=gray77 lvref=33
 html=my_html
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart17 State Population Growth Comparison
![](/img/Robert SAS Graphics/population_graph.png)

```sas
%let name=population_graph;
filename odsout '.';

data my_data (keep = statecode population_1910 population_1920 population_1930 
 population_1940 population_1950 population_1960 population_1970 
 population_1980 population_1990 population_2000 population_2010); 
 set sashelp.us_data (where=(statecode in ('NC' 'SC' 'VA')));
run;

proc transpose data=my_data out=my_data; 
by statecode notsorted;
run;

data my_data (keep = statecode year population label_var); set my_data;
year=.; year=scan(_name_,2,'_');
population=col1;
if year=2010 then label_var=statecode;
run;

goptions device=png;
goptions noborder;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Population Comparison") style=htmlblue;

goptions gunit=pct ftitle='albany amt/bold' ftext='albany amt' htitle=4.5 htext=2.5 ctext=gray33;

symbol1 value=dot height=2.5 interpol=join width=3 color=cx1d75b2
 pointlabel=(font='albany amt/bold' height=2.5 color=gray55 position=middle justify=right "#label_var");

symbol2 value=dot height=2.5 interpol=join width=3 color=cx2ba02b
 pointlabel=(font='albany amt/bold' height=2.5 color=gray55 position=middle justify=right "#label_var");

symbol3 value=dot height=2.5 interpol=join width=3 color=cxd52728
 pointlabel=(font='albany amt/bold' height=2.5 color=gray55 position=middle justify=right "#label_var");

axis1 label=none order=(0 to 10000000 by 2000000) major=none minor=none offset=(0,0);

axis2 label=none order=(1900 to 2020 by 20) major=none minor=none offset=(0,0);

legend1 label=none position=(top center);

title1 ls=1.5 "State Population Growth Comparison";

proc gplot data=my_data;
format population comma12.0;
plot population*year=statecode / overlay
 vaxis=axis1 haxis=axis2 noframe
 autovref cvref=graydd
 autohref chref=graydd
 legend=legend1
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)

## Chart18 Catalyst Experiment
![](/img/Robert SAS Graphics/catalyst_3d_surface.png)

```sas
%let name=catalyst_3d_surface;
filename odsout '.';

/*
Slightly modified version of example from SAS/Graph V6 Examples book (Example 6)

trivia: Note that this example was originally shown in the
V6 SAS/Graph examples book (about 30 years ago). I made only
minimal changes to the code, to have it use more modern
smooth-edged (anti-aliased) fonts, and more vibrant colors.
*/

data results;
input temp catalyst yield;
datalines;
 60    0.000    6.83888
 60    0.002    7.15841
 60    0.004    7.43373
 60    0.006    7.66581
 60    0.008    7.85560
 60    0.010    8.00404
 60    0.012    8.11210
 60    0.014    8.18072
 60    0.016    8.21087
 60    0.018    8.20349
 60    0.020    8.15953
 60    0.022    8.07996
 60    0.024    7.96572
 60    0.026    7.81777
 60    0.028    7.63706
 60    0.030    7.42454
 60    0.032    7.18118
 60    0.034    6.90791
 60    0.036    6.60570
 60    0.038    6.27550
 60    0.040    5.91826
 60    0.042    5.53494
 60    0.044    5.12649
 60    0.046    4.69386
 60    0.048    4.23800
 60    0.050    3.75988
 60    0.052    3.26044
 60    0.054    2.74063
 60    0.056    2.20142
 60    0.058    1.64375
 60    0.060    1.06858
 60    0.062    0.47685
 60    0.064    0.00000
 60    0.066    0.00000
 60    0.068    0.00000
 60    0.070    0.00000
 60    0.072    0.00000
 60    0.074    0.00000
 60    0.076    0.00000
 60    0.078    0.00000
 60    0.080    0.00000
 62    0.000    7.15487
 62    0.002    7.48995
 62    0.004    7.78180
 62    0.006    8.03137
 62    0.008    8.23962
 62    0.010    8.40750
 62    0.012    8.53596
 62    0.014    8.62596
 62    0.016    8.67845
 62    0.018    8.69438
 62    0.020    8.67471
 62    0.022    8.62039
 62    0.024    8.53237
 62    0.026    8.41162
 62    0.028    8.25907
 62    0.030    8.07569
 62    0.032    7.86243
 62    0.034    7.62024
 62    0.036    7.35007
 62    0.038    7.05288
 62    0.040    6.72963
 62    0.042    6.38126
 62    0.044    6.00873
 62    0.046    5.61299
 62    0.048    5.19500
 62    0.050    4.75571
 62    0.052    4.29607
 62    0.054    3.81704
 62    0.056    3.31957
 62    0.058    2.80461
 62    0.060    2.27312
 62    0.062    1.72605
 62    0.064    1.16436
 62    0.066    0.58899
 62    0.068    0.00090
 62    0.070    0.00000
 62    0.072    0.00000
 62    0.074    0.00000
 62    0.076    0.00000
 62    0.078    0.00000
 62    0.080    0.00000
 64    0.000    7.38742
 64    0.002    7.73663
 64    0.004    8.04358
 64    0.006    8.30922
 64    0.008    8.53451
 64    0.010    8.72040
 64    0.012    8.86784
 64    0.014    8.97779
 64    0.016    9.05120
 64    0.018    9.08902
 64    0.020    9.09221
 64    0.022    9.06172
 64    0.024    8.99850
 64    0.026    8.90351
 64    0.028    8.77771
 64    0.030    8.62203
 64    0.032    8.43745
 64    0.034    8.22491
 64    0.036    7.98536
 64    0.038    7.71976
 64    0.040    7.42906
 64    0.042    7.11422
 64    0.044    6.77619
 64    0.046    6.41592
 64    0.048    6.03437
 64    0.050    5.63249
 64    0.052    5.21123
 64    0.054    4.77155
 64    0.056    4.31439
 64    0.058    3.84072
 64    0.060    3.35149
 64    0.062    2.84765
 64    0.064    2.33015
 64    0.066    1.79995
 64    0.068    1.25800
 64    0.070    0.70526
 64    0.072    0.14267
 64    0.074    0.00000
 64    0.076    0.00000
 64    0.078    0.00000
 64    0.080    0.00000
 66    0.000    7.54188
 66    0.002    7.90380
 66    0.004    8.22442
 66    0.006    8.50471
 66    0.008    8.74561
 66    0.010    8.94809
 66    0.012    9.11308
 66    0.014    9.24156
 66    0.016    9.33446
 66    0.018    9.39274
 66    0.020    9.41737
 66    0.022    9.40928
 66    0.024    9.36944
 66    0.026    9.29880
 66    0.028    9.19831
 66    0.030    9.06892
 66    0.032    8.91159
 66    0.034    8.72727
 66    0.036    8.51692
 66    0.038    8.28148
 66    0.040    8.02192
 66    0.042    7.73918
 66    0.044    7.43422
 66    0.046    7.10800
 66    0.048    6.76146
 66    0.050    6.39556
 66    0.052    6.01125
 66    0.054    5.60949
 66    0.056    5.19124
 66    0.058    4.75743
 66    0.060    4.30903
 66    0.062    3.84699
 66    0.064    3.37227
 66    0.066    2.88581
 66    0.068    2.38858
 66    0.070    1.88151
 66    0.072    1.36558
 66    0.074    0.84173
 66    0.076    0.31091
 66    0.078    0.00000
 66    0.080    0.00000
 68    0.000    7.62360
 68    0.002    7.99680
 68    0.004    8.32967
 68    0.006    8.62318
 68    0.008    8.87827
 68    0.010    9.09591
 68    0.012    9.27703
 68    0.014    9.42260
 68    0.016    9.53358
 68    0.018    9.61090
 68    0.020    9.65554
 68    0.022    9.66843
 68    0.024    9.65054
 68    0.026    9.60282
 68    0.028    9.52621
 68    0.030    9.42168
 68    0.032    9.29018
 68    0.034    9.13266
 68    0.036    8.95008
 68    0.038    8.74339
 68    0.040    8.51353
 68    0.042    8.26147
 68    0.044    7.98817
 68    0.046    7.69456
 68    0.048    7.38161
 68    0.050    7.05027
 68    0.052    6.70149
 68    0.054    6.33623
 68    0.056    5.95544
 68    0.058    5.56007
 68    0.060    5.15108
 68    0.062    4.72942
 68    0.064    4.29605
 68    0.066    3.85191
 68    0.068     3.3980
 68    0.070     2.9352
 68    0.072     2.4645
 68    0.074     1.9868
 68    0.076     1.5032
 68    0.078     1.0145
 68    0.080     0.5217
 70    0.000     7.6379
 70    0.002     8.0210
 70    0.004     8.3647
 70    0.006     8.6700
 70    0.008     8.9378
 70    0.010     9.1692
 70    0.012     9.3650
 70    0.014     9.5263
 70    0.016     9.6539
 70    0.018     9.7488
 70    0.020     9.8121
 70    0.022     9.8445
 70    0.024     9.8471
 70    0.026     9.8209
 70    0.028     9.7668
 70    0.030     9.6857
 70    0.032     9.5786
 70    0.034     9.4464
 70    0.036     9.2902
 70    0.038     9.1108
 70    0.040     8.9092
 70    0.042     8.6864
 70    0.044     8.4434
 70    0.046     8.1809
 70    0.048     7.9002
 70    0.050     7.6020
 70    0.052     7.2873
 70    0.054     6.9571
 70    0.056     6.6123
 70    0.058     6.2540
 70    0.060     5.8830
 70    0.062     5.5003
 70    0.064     5.1068
 70    0.066     4.7036
 70    0.068     4.2915
 70    0.070     3.8715
 70    0.072     3.4446
 70    0.074     3.0117
 70    0.076     2.5738
 70    0.078     2.1319
 70    0.080     1.6868
 72    0.000     7.5902
 72    0.002     7.9817
 72    0.004     8.3348
 72    0.006     8.6504
 72    0.008     8.9296
 72    0.010     9.1733
 72    0.012     9.3824
 72    0.014     9.5579
 72    0.016     9.7008
 72    0.018     9.8119
 72    0.020     9.8923
 72    0.022     9.9428
 72    0.024     9.9646
 72    0.026     9.9584
 72    0.028     9.9253
 72    0.030     9.8662
 72    0.032     9.7821
 72    0.034     9.6739
 72    0.036     9.5426
 72    0.038     9.3891
 72    0.040     9.2144
 72    0.042     9.0194
 72    0.044     8.8051
 72    0.046     8.5725
 72    0.048     8.3225
 72    0.050     8.0560
 72    0.052     7.7740
 72    0.054     7.4774
 72    0.056     7.1673
 72    0.058     6.8445
 72    0.060     6.5101
 72    0.062     6.1649
 72    0.064     5.8100
 72    0.066     5.4462
 72    0.068     5.0745
 72    0.070     4.6960
 72    0.072     4.3115
 72    0.074     3.9219
 72    0.076     3.5283
 72    0.078     3.1316
 72    0.080     2.7328
 74    0.000     7.4857
 74    0.002     7.8842
 74    0.004     8.2453
 74    0.006     8.5699
 74    0.008     8.8590
 74    0.010     9.1136
 74    0.012     9.3345
 74    0.014     9.5229
 74    0.016     9.6795
 74    0.018     9.8054
 74    0.020     9.9015
 74    0.022     9.9688
 74    0.024    10.0082
 74    0.026    10.0207
 74    0.028    10.0072
 74    0.030     9.9687
 74    0.032     9.9062
 74    0.034     9.8205
 74    0.036     9.7127
 74    0.038     9.5836
 74    0.040     9.4344
 74    0.042     9.2658
 74    0.044     9.0789
 74    0.046     8.8746
 74    0.048     8.6538
 74    0.050     8.4176
 74    0.052     8.1669
 74    0.054     7.9026
 74    0.056     7.6256
 74    0.058     7.3370
 74    0.060     7.0377
 74    0.062     6.7287
 74    0.064     6.4108
 74    0.066     6.0850
 74    0.068     5.7524
 74    0.070     5.4138
 74    0.072     5.0703
 74    0.074     4.7227
 74    0.076     4.3720
 74    0.078     4.0191
 74    0.080     3.6651
 76    0.000     7.3299
 76    0.002     7.7339
 76    0.004     8.1016
 76    0.006     8.4337
 76    0.008     8.7313
 76    0.010     8.9953
 76    0.012     9.2267
 76    0.014     9.4265
 76    0.016     9.5955
 76    0.018     9.7347
 76    0.020     9.8451
 76    0.022     9.9277
 76    0.024     9.9834
 76    0.026    10.0131
 76    0.028    10.0178
 76    0.030     9.9984
 76    0.032     9.9560
 76    0.034     9.8914
 76    0.036     9.8057
 76    0.038     9.6997
 76    0.040     9.5744
 76    0.042     9.4308
 76    0.044     9.2699
 76    0.046     9.0925
 76    0.048     8.8997
 76    0.050     8.6923
 76    0.052     8.4714
 76    0.054     8.2379
 76    0.056     7.9927
 76    0.058     7.7369
 76    0.060     7.4713
 76    0.062     7.1969
 76    0.064     6.9146
 76    0.066     6.6255
 76    0.068     6.3305
 76    0.070     6.0305
 76    0.072     5.7264
 76    0.074     5.4193
 76    0.076     5.1101
 76    0.078     4.7997
 76    0.080     4.4891
 78    0.000     7.1280
 78    0.002     7.5362
 78    0.004     7.9090
 78    0.006     8.2472
 78    0.008     8.5519
 78    0.010     8.8240
 78    0.012     9.0644
 78    0.014     9.2741
 78    0.016     9.4540
 78    0.018     9.6052
 78    0.020     9.7285
 78    0.022     9.8249
 78    0.024     9.8954
 78    0.026     9.9409
 78    0.028     9.9624
 78    0.030     9.9608
 78    0.032     9.9370
 78    0.034     9.8921
 78    0.036     9.8270
 78    0.038     9.7427
 78    0.040     9.6400
 78    0.042     9.5200
 78    0.044     9.3835
 78    0.046     9.2316
 78    0.048     9.0653
 78    0.050     8.8853
 78    0.052     8.6928
 78    0.054    8.48870
 78    0.056    8.27388
 78    0.058    8.04933
 78    0.060    7.81601
 78    0.062    7.57487
 78    0.064    7.32687
 78    0.066    7.07295
 78    0.068    6.81407
 78    0.070    6.55119
 78    0.072    6.28525
 78    0.074    6.01722
 78    0.076    5.74804
 78    0.078    5.47867
 78    0.080    5.21006
 80    0.000    6.88546
 80    0.002    7.29642
 80    0.004    7.67288
 80    0.006    8.01578
 80    0.008    8.32610
 80    0.010    8.60477
 80    0.012    8.85276
 80    0.014    9.07101
 80    0.016    9.26048
 80    0.018    9.42213
 80    0.020    9.55690
 80    0.022    9.66575
 80    0.024    9.74964
 80    0.026    9.80951
 80    0.028    9.84632
 80    0.030    9.86103
 80    0.032    9.85459
 80    0.034    9.82795
 80    0.036    9.78206
 80    0.038    9.71788
 80    0.040    9.63636
 80    0.042    9.53845
 80    0.044    9.42512
 80    0.046    9.29731
 80    0.048    9.15597
 80    0.050    9.00206
 80    0.052    8.83654
 80    0.054    8.66035
 80    0.056    8.47445
 80    0.058    8.27979
 80    0.060    8.07733
 80    0.062    7.86802
 80    0.064    7.65282
 80    0.066    7.43267
 80    0.068    7.20853
 80    0.070    6.98136
 80    0.072    6.75210
 80    0.074    6.52172
 80    0.076    6.29116
 80    0.078    6.06138
 80    0.080    5.83333
 82    0.000    6.60756
 82    0.002    7.01983
 82    0.004    7.39856
 82    0.006    7.74472
 82    0.008    8.05925
 82    0.010    8.34311
 82    0.012    8.59725
 82    0.014    8.82263
 82    0.016    9.02020
 82    0.018    9.19091
 82    0.020    9.33572
 82    0.022    9.45558
 82    0.024    9.55144
 82    0.026    9.62426
 82    0.028    9.67499
 82    0.030    9.70458
 82    0.032    9.71399
 82    0.034    9.70418
 82    0.036    9.67608
 82    0.038    9.63067
 82    0.040    9.56889
 82    0.042    9.49169
 82    0.044    9.40003
 82    0.046    9.29486
 82    0.048    9.17714
 82    0.050    9.04782
 82    0.052    8.90785
 82    0.054    8.75819
 82    0.056    8.59978
 82    0.058    8.43359
 82    0.060    8.26057
 82    0.062    8.08166
 82    0.064    7.89783
 82    0.066    7.71003
 82    0.068    7.51921
 82    0.070    7.32632
 82    0.072    7.13232
 82    0.074    6.93816
 82    0.076    6.74479
 82    0.078    6.55317
 82    0.080    6.36426
 84    0.000    6.29966
 84    0.002    6.71181
 84    0.004    7.09140
 84    0.006    7.43937
 84    0.008    7.75670
 84    0.010    8.04432
 84    0.012    8.30319
 84    0.014    8.53427
 84    0.016    8.73851
 84    0.018    8.91687
 84    0.020    9.07029
 84    0.022    9.19973
 84    0.024    9.30614
 84    0.026    9.39048
 84    0.028    9.45370
 84    0.030    9.49676
 84    0.032    9.52060
 84    0.034    9.52619
 84    0.036    9.51446
 84    0.038    9.48639
 84    0.040    9.44292
 84    0.042    9.38500
 84    0.044    9.31359
 84    0.046    9.22965
 84    0.048    9.13412
 84    0.050    9.02796
 84    0.052    8.91212
 84    0.054    8.78755
 84    0.056    8.65522
 84    0.058    8.51607
 84    0.060    8.37106
 84    0.062    8.22113
 84    0.064    8.06725
 84    0.066    7.91037
 84    0.068    7.75143
 84    0.070    7.59141
 84    0.072    7.43123
 84    0.074    7.27187
 84    0.076    7.11428
 84    0.078    6.95940
 84    0.080    6.80820
 86    0.000    5.96710
 86    0.002    6.37771
 86    0.004    6.75672
 86    0.006    7.10509
 86    0.008    7.42379
 86    0.010    7.71374
 86    0.012    7.97593
 86    0.014    8.21128
 86    0.016    8.42077
 86    0.018    8.60534
 86    0.020    8.76595
 86    0.022    8.90355
 86    0.024    9.01909
 86    0.026    9.11352
 86    0.028    9.18781
 86    0.030    9.24290
 86    0.032    9.27975
 86    0.034    9.29931
 86    0.036    9.30254
 86    0.038    9.29038
 86    0.040    9.26380
 86    0.042    9.22373
 86    0.044    9.17115
 86    0.046    9.10700
 86    0.048    9.03224
 86    0.050    8.94781
 86    0.052    8.85468
 86    0.054    8.75379
 86    0.056    8.64610
 86    0.058    8.53257
 86    0.060    8.41414
 86    0.062    8.29177
 86    0.064    8.16642
 86    0.066    8.03903
 86    0.068    7.91056
 86    0.070    7.78197
 86    0.072    7.65420
 86    0.074    7.52822
 86    0.076    7.40497
 86    0.078    7.28540
 86    0.080    7.17048
 88    0.000    5.61523
 88    0.002    6.02286
 88    0.004    6.39988
 88    0.006    6.74723
 88    0.008    7.06586
 88    0.010    7.35673
 88    0.012    7.62079
 88    0.014    7.85900
 88    0.016    8.07231
 88    0.018    8.26168
 88    0.020    8.42805
 88    0.022    8.57237
 88    0.024    8.69562
 88    0.026    8.79873
 88    0.028    8.88266
 88    0.030    8.94836
 88    0.032    8.99679
 88    0.034    9.02890
 88    0.036    9.04565
 88    0.038    9.04798
 88    0.040    9.03686
 88    0.042    9.01323
 88    0.044    8.97805
 88    0.046    8.93227
 88    0.048    8.87685
 88    0.050    8.81273
 88    0.052    8.74088
 88    0.054    8.66224
 88    0.056    8.57778
 88    0.058    8.48843
 88    0.060    8.39517
 88    0.062    8.29893
 88    0.064    8.20068
 88    0.066    8.10136
 88    0.068    8.00193
 88    0.070    7.90335
 88    0.072    7.80656
 88    0.074    7.71253
 88    0.076    7.62220
 88    0.078    7.53653
 88    0.080    7.45646
 90    0.000    5.24938
 90    0.002    5.65262
 90    0.004    6.02621
 90    0.006    6.37111
 90    0.008    6.68826
 90    0.010    6.97862
 90    0.012    7.24314
 90    0.014    7.48278
 90    0.016    7.69849
 90    0.018    7.89122
 90    0.020    8.06192
 90    0.022    8.21156
 90    0.024    8.34108
 90    0.026    8.45143
 90    0.028    8.54358
 90    0.030    8.61847
 90    0.032    8.67706
 90    0.034    8.72030
 90    0.036    8.74914
 90    0.038    8.76454
 90    0.040    8.76746
 90    0.042    8.75883
 90    0.044    8.73963
 90    0.046    8.71080
 90    0.048    8.67329
 90    0.050    8.62806
 90    0.052    8.57607
 90    0.054    8.51826
 90    0.056    8.45559
 90    0.058    8.38901
 90    0.060    8.31948
 90    0.062    8.24794
 90    0.064    8.17537
 90    0.066    8.10269
 90    0.068    8.03088
 90    0.070    7.96089
 90    0.072    7.89366
 90    0.074    7.83015
 90    0.076    7.77131
 90    0.078    7.71811
 90    0.080    7.67148
 92    0.000    4.87490
 92    0.002    5.27232
 92    0.004    5.64107
 92    0.006    5.98209
 92    0.008    6.29633
 92    0.010    6.58475
 92    0.012    6.84831
 92    0.014    7.08795
 92    0.016    7.30463
 92    0.018    7.49930
 92    0.020    7.67292
 92    0.022    7.82644
 92    0.024    7.96081
 92    0.026    8.07699
 92    0.028    8.17593
 92    0.030    8.25858
 92    0.032    8.32590
 92    0.034    8.37884
 92    0.036    8.41836
 92    0.038    8.44540
 92    0.040    8.46093
 92    0.042    8.46588
 92    0.044    8.46123
 92    0.046    8.44792
 92    0.048    8.42691
 92    0.050    8.39914
 92    0.052    8.36558
 92    0.054    8.32717
 92    0.056    8.28487
 92    0.058    8.23963
 92    0.060    8.19241
 92    0.062    8.14416
 92    0.064    8.09583
 92    0.066    8.04838
 92    0.068    8.00276
 92    0.070    7.95993
 92    0.072    7.92083
 92    0.074    7.88642
 92    0.076    7.85766
 92    0.078    7.83549
 92    0.080    7.82088
 94    0.000    4.49713
 94    0.002    4.88731
 94    0.004    5.24979
 94    0.006    5.58550
 94    0.008    5.89542
 94    0.010    6.18048
 94    0.012    6.44164
 94    0.014    6.67986
 94    0.016    6.89608
 94    0.018    7.09127
 94    0.020    7.26638
 94    0.022    7.42236
 94    0.024    7.56016
 94    0.026    7.68073
 94    0.028    7.78504
 94    0.030    7.87403
 94    0.032    7.94866
 94    0.034    8.00988
 94    0.036    8.05864
 94    0.038    8.09590
 94    0.040    8.12261
 94    0.042    8.13973
 94    0.044    8.14820
 94    0.046    8.14899
 94    0.048    8.14304
 94    0.050    8.13131
 94    0.052    8.11475
 94    0.054    8.09432
 94    0.056    8.07097
 94    0.058    8.04565
 94    0.060    8.01932
 94    0.062    7.99292
 94    0.064    7.96742
 94    0.066    7.94377
 94    0.068    7.92291
 94    0.070    7.90581
 94    0.072    7.89342
 94    0.074    7.88669
 94    0.076    7.88657
 94    0.078    7.89402
 94    0.080    7.90999
 96    0.000    4.12141
 96    0.002    4.50293
 96    0.004    4.85771
 96    0.006    5.18670
 96    0.008    5.49086
 96    0.010    5.77113
 96    0.012    6.02847
 96    0.014    6.26385
 96    0.016    6.47820
 96    0.018    6.67248
 96    0.020    6.84765
 96    0.022    7.00466
 96    0.024    7.14446
 96    0.026    7.26801
 96    0.028    7.37626
 96    0.030    7.47016
 96    0.032    7.55067
 96    0.034    7.61875
 96    0.036    7.67533
 96    0.038    7.72138
 96    0.040    7.75786
 96    0.042    7.78571
 96    0.044    7.80588
 96    0.046    7.81934
 96    0.048    7.82704
 96    0.050    7.82992
 96    0.052    7.82894
 96    0.054    7.82506
 96    0.056    7.81923
 96    0.058    7.81240
 96    0.060    7.80553
 96    0.062    7.79957
 96    0.064    7.79547
 96    0.066    7.79419
 96    0.068    7.79668
 96    0.070    7.80389
 96    0.072    7.81678
 96    0.074    7.83630
 96    0.076    7.86340
 96    0.078    7.89904
 96    0.080    7.94417
 98    0.000    3.75310
 98    0.002    4.12453
 98    0.004    4.47018
 98    0.006    4.79102
 98    0.008    5.08799
 98    0.010    5.36206
 98    0.012    5.61416
 98    0.014    5.84526
 98    0.016    6.05631
 98    0.018    6.24826
 98    0.020    6.42207
 98    0.022    6.57869
 98    0.024    6.71907
 98    0.026    6.84417
 98    0.028    6.95493
 98    0.030    7.05232
 98    0.032    7.13729
 98    0.034    7.21079
 98    0.036    7.27377
 98    0.038    7.32719
 98    0.040    7.37201
 98    0.042    7.40916
 98    0.044    7.43962
 98    0.046    7.46432
 98    0.048    7.48423
 98    0.050    7.50030
 98    0.052    7.51349
 98    0.054    7.52473
 98    0.056    7.53500
 98    0.058    7.54524
 98    0.060    7.55640
 98    0.062    7.56945
 98    0.064    7.58533
 98    0.066    7.60499
 98    0.068    7.62940
 98    0.070    7.65949
 98    0.072    7.69624
 98    0.074    7.74058
 98    0.076    7.79348
 98    0.078    7.85589
 98    0.080    7.92876
100    0.000    3.39752
100    0.002    3.75743
100    0.004    4.09255
100    0.006    4.40381
100    0.008    4.69217
100    0.010    4.95860
100    0.012    5.20404
100    0.014    5.42944
100    0.016    5.63577
100    0.018    5.82396
100    0.020    5.99498
100    0.022    6.14978
100    0.024    6.28932
100    0.026    6.41454
100    0.028    6.52640
100    0.030    6.62585
100    0.032    6.71385
100    0.034    6.79136
100    0.036    6.85931
100    0.038    6.91867
100    0.040    6.97040
100    0.042    7.01544
100    0.044    7.05475
100    0.046    7.08927
100    0.048    7.11998
100    0.050    7.14781
100    0.052    7.17372
100    0.054    7.19867
100    0.056    7.22361
100    0.058    7.24949
100    0.060    7.27727
100    0.062    7.30790
100    0.064    7.34233
100    0.066    7.38152
100    0.068    7.42641
100    0.070    7.47797
100    0.072    7.53715
100    0.074    7.60490
100    0.076    7.68217
100    0.078    7.76992
100    0.080    7.86910
102    0.000    3.06003
102    0.002    3.40700
102    0.004    3.73014
102    0.006    4.03040
102    0.008    4.30874
102    0.010    4.56610
102    0.012    4.80345
102    0.014    5.02173
102    0.016    5.22191
102    0.018    5.40492
102    0.020    5.57173
102    0.022    5.72329
102    0.024    5.86055
102    0.026    5.98447
102    0.028    6.09600
102    0.030    6.19609
102    0.032    6.28570
102    0.034    6.36578
102    0.036    6.43729
102    0.038    6.50117
102    0.040    6.55838
102    0.042    6.60988
102    0.044    6.65661
102    0.046    6.69954
102    0.048    6.73961
102    0.050    6.77778
102    0.052    6.81500
102    0.054    6.85223
102    0.056    6.89042
102    0.058    6.93052
102    0.060    6.97348
102    0.062    7.02027
102    0.064    7.07182
102    0.066    7.12911
102    0.068    7.19307
102    0.070    7.26467
102    0.072    7.34485
102    0.074    7.43458
102    0.076    7.53480
102    0.078    7.64646
102    0.080    7.77053
104    0.000    2.74596
104    0.002    3.07857
104    0.004    3.38832
104    0.006    3.67615
104    0.008    3.94303
104    0.010    4.18991
104    0.012    4.41774
104    0.014    4.62748
104    0.016    4.82008
104    0.018    4.99648
104    0.020    5.15766
104    0.022    5.30455
104    0.024    5.43812
104    0.026    5.55931
104    0.028    5.66908
104    0.030    5.76839
104    0.032    5.85818
104    0.034    5.93942
104    0.036    6.01304
104    0.038    6.08002
104    0.040    6.14129
104    0.042    6.19782
104    0.044    6.25056
104    0.046    6.30046
104    0.048    6.34848
104    0.050    6.39556
104    0.052    6.44267
104    0.054    6.49075
104    0.056    6.54076
104    0.058    6.59365
104    0.060    6.65037
104    0.062    6.71189
104    0.064    6.77915
104    0.066    6.85311
104    0.068    6.93471
104    0.070    7.02492
104    0.072    7.12469
104    0.074    7.23497
104    0.076    7.35671
104    0.078    7.49087
104    0.080    7.63840
106    0.000    2.46067
106    0.002    2.77749
106    0.004    3.07241
106    0.006    3.34639
106    0.008    3.60039
106    0.010    3.83536
106    0.012    4.05225
106    0.014    4.25202
106    0.016    4.43562
106    0.018    4.60399
106    0.020    4.75811
106    0.022    4.89891
106    0.024    5.02735
106    0.026    5.14440
106    0.028    5.25099
106    0.030    5.34808
106    0.032    5.43663
106    0.034    5.51760
106    0.036    5.59192
106    0.038    5.66057
106    0.040    5.72448
106    0.042    5.78462
106    0.044    5.84194
106    0.046    5.89739
106    0.048    5.95192
106    0.050    6.00649
106    0.052    6.06205
106    0.054    6.11956
106    0.056    6.17997
106    0.058    6.24423
106    0.060    6.31329
106    0.062    6.38812
106    0.064    6.46966
106    0.066    6.55886
106    0.068    6.65668
106    0.070    6.76408
106    0.072    6.88201
106    0.074    7.01141
106    0.076    7.15325
106    0.078    7.30847
106    0.080    7.47804
108    0.000    2.20949
108    0.002    2.50909
108    0.004    2.78777
108    0.006    3.04648
108    0.008    3.28617
108    0.010    3.50780
108    0.012    3.71233
108    0.014    3.90070
108    0.016    4.07387
108    0.018    4.23279
108    0.020    4.37842
108    0.022    4.51171
108    0.024    4.63361
108    0.026    4.74507
108    0.028    4.84706
108    0.030    4.94052
108    0.032    5.02640
108    0.034    5.10567
108    0.036    5.17927
108    0.038    5.24816
108    0.040    5.31329
108    0.042    5.37561
108    0.044    5.43608
108    0.046    5.49565
108    0.048    5.55528
108    0.050    5.61591
108    0.052    5.67851
108    0.054    5.74402
108    0.056    5.81340
108    0.058    5.88760
108    0.060    5.96758
108    0.062    6.05429
108    0.064    6.14868
108    0.066    6.25171
108    0.068    6.36433
108    0.070    6.48749
108    0.072    6.62214
108    0.074    6.76925
108    0.076    6.92976
108    0.078    7.10463
108    0.080    7.29481
110    0.000    1.99776
110    0.002    2.27872
110    0.004    2.53973
110    0.006    2.78174
110    0.008    3.00570
110    0.010    3.21258
110    0.012    3.40331
110    0.014    3.57886
110    0.016    3.74018
110    0.018    3.88822
110    0.020    4.02394
110    0.022    4.14829
110    0.024    4.26222
110    0.026    4.36668
110    0.028    4.46264
110    0.030    4.55104
110    0.032    4.63283
110    0.034    4.70898
110    0.036    4.78043
110    0.038    4.84813
110    0.040    4.91305
110    0.042    4.97613
110    0.044    5.03833
110    0.046    5.10060
110    0.048    5.16390
110    0.050    5.22917
110    0.052    5.29737
110    0.054    5.36947
110    0.056    5.44639
110    0.058    5.52912
110    0.060    5.61858
110    0.062    5.71575
110    0.064    5.82157
110    0.066    5.93699
110    0.068    6.06298
110    0.070    6.20048
110    0.072    6.35044
110    0.074    6.51383
110    0.076    6.69159
110    0.078    6.88467
110    0.080    7.09404
112    0.000    1.83083
112    0.002    2.09172
112    0.004    2.33364
112    0.006    2.55752
112    0.008    2.76433
112    0.010    2.95502
112    0.012    3.13054
112    0.014    3.29185
112    0.016    3.43989
112    0.018    3.57563
112    0.020    3.70001
112    0.022    3.81399
112    0.024    3.91853
112    0.026    4.01457
112    0.028    4.10307
112    0.030    4.18498
112    0.032    4.26126
112    0.034    4.33286
112    0.036    4.40073
112    0.038    4.46583
112    0.040    4.52912
112    0.042    4.59153
112    0.044    4.65403
112    0.046    4.71758
112    0.048    4.78312
112    0.050    4.85161
112    0.052    4.92400
112    0.054    5.00124
112    0.056    5.08429
112    0.058    5.17411
112    0.060    5.27164
112    0.062    5.37784
112    0.064    5.49366
112    0.066    5.62006
112    0.068    5.75799
112    0.070    5.90840
112    0.072    6.07225
112    0.074    6.25049
112    0.076    6.44407
112    0.078    6.65395
112    0.080    6.88108
114    0.000    1.71404
114    0.002    1.95344
114    0.004    2.17484
114    0.006    2.37917
114    0.008    2.56741
114    0.010    2.74048
114    0.012    2.89937
114    0.014    3.04500
114    0.016    3.17835
114    0.018    3.30035
114    0.020    3.41197
114    0.022    3.51417
114    0.024    3.60788
114    0.026    3.69407
114    0.028    3.77369
114    0.030    3.84769
114    0.032    3.91703
114    0.034    3.98266
114    0.036    4.04554
114    0.038    4.10661
114    0.040    4.16683
114    0.042    4.22715
114    0.044    4.28854
114    0.046    4.35193
114    0.048    4.41829
114    0.050    4.48856
114    0.052    4.56371
114    0.054    4.64469
114    0.056    4.73244
114    0.058    4.82792
114    0.060    4.93209
114    0.062    5.04590
114    0.064    5.17030
114    0.066    5.30625
114    0.068    5.45470
114    0.070    5.61660
114    0.072    5.79290
114    0.074    5.98457
114    0.076    6.19255
114    0.078    6.41780
114    0.080    6.66127
116    0.000    1.65273
116    0.002    1.86922
116    0.004    2.06867
116    0.006    2.25204
116    0.008    2.42026
116    0.010    2.57431
116    0.012    2.71512
116    0.014    2.84367
116    0.016    2.96089
116    0.018    3.06774
116    0.020    3.16518
116    0.022    3.25415
116    0.024    3.33562
116    0.026    3.41054
116    0.028    3.47985
116    0.030    3.54452
116    0.032    3.60549
116    0.034    3.66373
116    0.036    3.72018
116    0.038    3.77579
116    0.040    3.83153
116    0.042    3.88834
116    0.044    3.94718
116    0.046    4.00899
116    0.048    4.07475
116    0.050    4.14539
116    0.052    4.22187
116    0.054    4.30515
116    0.056    4.39617
116    0.058    4.49590
116    0.060    4.60529
116    0.062    4.72528
116    0.064    4.85683
116    0.066    5.00091
116    0.068    5.15845
116    0.070    5.33041
116    0.072    5.51775
116    0.074    5.72142
116    0.076    5.94238
116    0.078    6.18157
116    0.080    6.43995
118    0.000    1.65225
118    0.002    1.84440
118    0.004    2.02049
118    0.006    2.18145
118    0.008    2.32825
118    0.010    2.46183
118    0.012    2.58316
118    0.014    2.69318
118    0.016    2.79285
118    0.018    2.88313
118    0.020    2.96495
118    0.022    3.03929
118    0.024    3.10709
118    0.026    3.16931
118    0.028    3.22689
118    0.030    3.28080
118    0.032    3.33199
118    0.034    3.38140
118    0.036    3.43000
118    0.038    3.47873
118    0.040    3.52856
118    0.042    3.58043
118    0.044    3.63530
118    0.046    3.69411
118    0.048    3.75784
118    0.050    3.82742
118    0.052    3.90381
118    0.054    3.98797
118    0.056    4.08084
118    0.058    4.18339
118    0.060    4.29656
118    0.062    4.42132
118    0.064    4.55860
118    0.066    4.70937
118    0.068    4.87458
118    0.070    5.05518
118    0.072    5.25213
118    0.074    5.46638
118    0.076    5.69889
118    0.078    5.95059
118    0.080    6.22247
120    0.000    1.71794
120    0.002    1.88433
120    0.004    2.03562
120    0.006    2.17276
120    0.008    2.29670
120    0.010    2.40840
120    0.012    2.50882
120    0.014    2.59890
120    0.016    2.67959
120    0.018    2.75186
120    0.020    2.81666
120    0.022    2.87493
120    0.024    2.92763
120    0.026    2.97572
120    0.028    3.02016
120    0.030    3.06188
120    0.032    3.10185
120    0.034    3.14102
120    0.036    3.18034
120    0.038    3.22077
120    0.040    3.26326
120    0.042    3.30877
120    0.044    3.35824
120    0.046    3.41264
120    0.048    3.47290
120    0.050    3.54000
120    0.052    3.61488
120    0.054    3.69849
120    0.056    3.79179
120    0.058    3.89573
120    0.060    4.01127
120    0.062    4.13935
120    0.064    4.28094
120    0.066    4.43699
120    0.068    4.60844
120    0.070    4.79626
120    0.072    5.00139
120    0.074    5.22480
120    0.076    5.46742
120    0.078    5.73023
120    0.080    6.01416
122    0.000    1.85514
122    0.002    1.99435
122    0.004    2.11942
122    0.006    2.23131
122    0.008    2.33097
122    0.010    2.41936
122    0.012    2.49744
122    0.014    2.56615
122    0.016    2.62645
122    0.018    2.67929
122    0.020    2.72562
122    0.022    2.76640
122    0.024    2.80259
122    0.026    2.83513
122    0.028    2.86498
122    0.030    2.89310
122    0.032    2.92043
122    0.034    2.94793
122    0.036    2.97656
122    0.038    3.00726
122    0.040    3.04099
122    0.042    3.07870
122    0.044    3.12136
122    0.046    3.16990
122    0.048    3.22529
122    0.050    3.28847
122    0.052    3.36041
122    0.054    3.44205
122    0.056    3.53435
122    0.058    3.63826
122    0.060    3.75474
122    0.062    3.88474
122    0.064    4.02921
122    0.066    4.18910
122    0.068    4.36537
122    0.070    4.55898
122    0.072    4.77087
122    0.074    5.00201
122    0.076    5.25333
122    0.078    5.52581
122    0.080    5.82038
124    0.000    2.06920
124    0.002    2.17979
124    0.004    2.27722
124    0.006    2.36243
124    0.008    2.43640
124    0.010    2.50006
124    0.012    2.55437
124    0.014    2.60028
124    0.016    2.63876
124    0.018    2.67074
124    0.020    2.69719
124    0.022    2.71906
124    0.024    2.73731
124    0.026    2.75287
124    0.028    2.76672
124    0.030    2.77980
124    0.032    2.79307
124    0.034    2.80748
124    0.036    2.82398
124    0.038    2.84352
124    0.040    2.86707
124    0.042    2.89557
124    0.044    2.92998
124    0.046    2.97125
124    0.048    3.02033
124    0.050    3.07818
124    0.052    3.14575
124    0.054    3.22400
124    0.056    3.31388
124    0.058    3.41633
124    0.060    3.53233
124    0.062    3.66281
124    0.064    3.80873
124    0.066    3.97105
124    0.068    4.15072
124    0.070    4.34869
124    0.072    4.56592
124    0.074    4.80336
124    0.076    5.06196
124    0.078    5.34267
124    0.080    5.64646
126    0.000    2.36546
126    0.002    2.44601
126    0.004    2.51437
126    0.006    2.57149
126    0.008    2.61832
126    0.010    2.65582
126    0.012    2.68494
126    0.014    2.70664
126    0.016    2.72187
126    0.018    2.73157
126    0.020    2.73672
126    0.022    2.73825
126    0.024    2.73712
126    0.026    2.73429
126    0.028    2.73071
126    0.030    2.72733
126    0.032    2.72511
126    0.034    2.72500
126    0.036    2.72795
126    0.038    2.73492
126    0.040    2.74686
126    0.042    2.76472
126    0.044    2.78946
126    0.046    2.82203
126    0.048    2.86338
126    0.050    2.91447
126    0.052    2.97625
126    0.054    3.04968
126    0.056    3.13571
126    0.058    3.23528
126    0.060    3.34937
126    0.062    3.47891
126    0.064    3.62486
126    0.066    3.78818
126    0.068    3.96982
126    0.070    4.17073
126    0.072    4.39187
126    0.074    4.63419
126    0.076    4.89864
126    0.078    5.18617
126    0.080    5.49775
128    0.000    2.74925
128    0.002    2.79834
128    0.004    2.83621
128    0.006    2.86380
128    0.008    2.88208
128    0.010    2.89200
128    0.012    2.89451
128    0.014    2.89057
128    0.016    2.88112
128    0.018    2.86712
128    0.020    2.84953
128    0.022    2.82930
128    0.024    2.80738
128    0.026    2.78473
128    0.028    2.76229
128    0.030    2.74103
128    0.032    2.72190
128    0.034    2.70584
128    0.036    2.69382
128    0.038    2.68678
128    0.040    2.68569
128    0.042    2.69148
128    0.044    2.70513
128    0.046    2.72757
128    0.048    2.75977
128    0.050    2.80268
128    0.052    2.85725
128    0.054    2.92443
128    0.056    3.00518
128    0.058    3.10046
128    0.060    3.21121
128    0.062    3.33838
128    0.064    3.48294
128    0.066    3.64584
128    0.068    3.82802
128    0.070    4.03045
128    0.072    4.25407
128    0.074    4.49984
128    0.076    4.76872
128    0.078    5.06165
128    0.080    5.37959
130    0.000    3.22593
130    0.002    3.24213
130    0.004    3.24808
130    0.006    3.24473
130    0.008    3.23304
130    0.010    3.21395
130    0.012    3.18842
130    0.014    3.15740
130    0.016    3.12186
130    0.018    3.08274
130    0.020    3.04099
130    0.022    2.99757
130    0.024    2.95343
130    0.026    2.90953
130    0.028    2.86682
130    0.030    2.82624
130    0.032    2.78877
130    0.034    2.75535
130    0.036    2.72692
130    0.038    2.70446
130    0.040    2.68891
130    0.042    2.68121
130    0.044    2.68234
130    0.046    2.69324
130    0.048    2.71486
130    0.050    2.74816
130    0.052    2.79409
130    0.054    2.85360
130    0.056    2.92765
130    0.058    3.01720
130    0.060    3.12319
130    0.062    3.24657
130    0.064    3.38831
130    0.066    3.54936
130    0.068    3.73066
130    0.070    3.93318
130    0.072    4.15786
130    0.074    4.40567
130    0.076    4.67754
130    0.078    4.97444
130    0.080    5.29733
132    0.000    3.80084
132    0.002    3.78273
132    0.004    3.75534
132    0.006    3.71961
132    0.008    3.67651
132    0.010    3.62699
132    0.012    3.57200
132    0.014    3.51249
132    0.016    3.44943
132    0.018    3.38375
132    0.020    3.31642
132    0.022    3.24839
132    0.024    3.18061
132    0.026    3.11403
132    0.028    3.04962
132    0.030    2.98831
132    0.032    2.93107
132    0.034    2.87886
132    0.036    2.83261
132    0.038    2.79329
132    0.040    2.76186
132    0.042    2.73925
132    0.044    2.72643
132    0.046    2.72436
132    0.048    2.73397
132    0.050    2.75624
132    0.052    2.79211
132    0.054    2.84253
132    0.056    2.90845
132    0.058    2.99084
132    0.060    3.09065
132    0.062    3.20882
132    0.064    3.34632
132    0.066    3.50409
132    0.068    3.68309
132    0.070    3.88427
132    0.072    4.10859
132    0.074    4.35700
132    0.076    4.63045
132    0.078    4.92990
132    0.080    5.25629
134    0.000    4.47932
134    0.002    4.42547
134    0.004    4.36331
134    0.006    4.29379
134    0.008    4.21786
134    0.010    4.13648
134    0.012    4.05060
134    0.014    3.96118
134    0.016    3.86917
134    0.018    3.77551
134    0.020    3.68117
134    0.022    3.58710
134    0.024    3.49426
134    0.026    3.40358
134    0.028    3.31604
134    0.030    3.23258
134    0.032    3.15415
134    0.034    3.08172
134    0.036    3.01622
134    0.038    2.95863
134    0.040    2.90988
134    0.042    2.87094
134    0.044    2.84275
134    0.046    2.82628
134    0.048    2.82247
134    0.050    2.83227
134    0.052    2.85665
134    0.054    2.89655
134    0.056    2.95293
134    0.058    3.02674
134    0.060    3.11894
134    0.062    3.23047
134    0.064    3.36230
134    0.066    3.51537
134    0.068    3.69064
134    0.070    3.88907
134    0.072    4.11160
134    0.074    4.35918
134    0.076    4.63279
134    0.078    4.93336
134    0.080    5.26184
136    0.000    5.26671
136    0.002    5.17570
136    0.004    5.07734
136    0.006    4.97260
136    0.008    4.86242
136    0.010    4.74776
136    0.012    4.62957
136    0.014    4.50881
136    0.016    4.38642
136    0.018    4.26336
136    0.020    4.14059
136    0.022    4.01906
136    0.024    3.89972
136    0.026    3.78352
136    0.028    3.67143
136    0.030    3.56438
136    0.032    3.46335
136    0.034    3.36927
136    0.036    3.28310
136    0.038    3.20580
136    0.040    3.13832
136    0.042    3.08162
136    0.044    3.03664
136    0.046    3.00434
136    0.048    2.98568
136    0.050    2.98160
136    0.052    2.99306
136    0.054    3.02102
136    0.056    3.06643
136    0.058    3.13024
136    0.060    3.21340
136    0.062    3.31687
136    0.064    3.44160
136    0.066    3.58855
136    0.068    3.75867
136    0.070    3.95291
136    0.072    4.17222
136    0.074    4.41757
136    0.076    4.68990
136    0.078    4.99016
136    0.080    5.31931
138    0.000    6.16835
138    0.002    6.03876
138    0.004    5.90279
138    0.006    5.76140
138    0.008    5.61554
138    0.010    5.46617
138    0.012    5.31425
138    0.014    5.16072
138    0.016    5.00653
138    0.018    4.85265
138    0.020    4.70002
138    0.022    4.54960
138    0.024    4.40234
138    0.026    4.25920
138    0.028    4.12113
138    0.030    3.98907
138    0.032    3.86400
138    0.034    3.74685
138    0.036    3.63859
138    0.038    3.54016
138    0.040    3.45253
138    0.042    3.37663
138    0.044    3.31344
138    0.046    3.26389
138    0.048    3.22895
138    0.050    3.20956
138    0.052    3.20669
138    0.054    3.22128
138    0.056    3.25429
138    0.058    3.30667
138    0.060    3.37937
138    0.062    3.47335
138    0.064    3.58957
138    0.066    3.72897
138    0.068    3.89251
138    0.070    4.08114
138    0.072    4.29581
138    0.074    4.53749
138    0.076    4.80712
138    0.078    5.10565
138    0.080    5.43405
140    0.000    7.18959
140    0.002    7.01999
140    0.004    6.84498
140    0.006    6.66552
140    0.008    6.48256
140    0.010    6.29706
140    0.012    6.10997
140    0.014    5.92225
140    0.016    5.73484
140    0.018    5.54871
140    0.020    5.36480
140    0.022    5.18406
140    0.024    5.00746
140    0.026    4.83595
140    0.028    4.67047
140    0.030    4.51199
140    0.032    4.36145
140    0.034    4.21982
140    0.036    4.08803
140    0.038    3.96705
140    0.040    3.85783
140    0.042    3.76133
140    0.044    3.67849
140    0.046    3.61027
140    0.048    3.55762
140    0.050    3.52150
140    0.052    3.50287
140    0.054    3.50266
140    0.056    3.52185
140    0.058    3.56138
140    0.060    3.62220
140    0.062    3.70527
140    0.064    3.81154
140    0.066    3.94196
140    0.068    4.09750
140    0.070    4.27910
140    0.072    4.48771
140    0.074    4.72429
140    0.076    4.98980
140    0.078    5.28518
140    0.080    5.61139
;
run;

%let nlevels=8;
%let colors='black gray cx1E90FF green lime gold orange red';

proc means data=results noprint min max;
var temp catalyst yield;
output out=range
  min=tempmin catmin yieldmin
  max=tempmax catmax yieldmax;
run;

data _null_;
   set range;
   call symput('tempmin', tempmin);
   call symput('tempmax', tempmax);
   call symput('catmin', catmin);
   call symput('catmax', catmax);
   call symput('yieldmin', yieldmin);
   call symput('yieldmax', yieldmax);
   call symput('floor', int(yieldmin-4));
   call symput('ceil',  int(yieldmax+2));
   call symput('step', (yieldmax-yieldmin)/&nlevels);
run;

proc sort data=results;
   by temp catalyst;
run;

data plane1 surf1;
length function color $8;
xsys='2'; ysys='2'; zsys='2';
drop catalyst temp yield ncol;
set results;
by temp;
x=temp; y=catalyst; z=&floor;
if first.temp then function='move';
else do;
   function='draw';
   ncol=min(&nlevels,int(1+(yield-&yieldmin)/&step));
   color=scan(&colors,ncol);
   end;
output plane1;
z=yield; 
output surf1;
run;

proc sort data=results;
   by catalyst temp;
run;

data plane2 surf2;
length function color $8;
xsys='2'; ysys='2'; zysy='2';
drop catalyst temp yield ncol;
set results;
by catalyst;
x=temp; y=catalyst; z=&floor;
if first.catalyst then function='move';
else do;
   function='draw';
   ncol=min(&nlevels,int(1+(yield-&yieldmin)/&step));
   color=scan(&colors,ncol);
   end;
output plane2;
z=yield; 
output surf2;
run;

data legend;
length function color $8;
xsys='2'; ysys='2'; zysy='2';
drop legend ncol;

do legend=&yieldmin to (&yieldmax-&step) by &step;
  x=&tempmin; y=&catmax; z=legend;
  function='poly'; style='solid';

  ncol=min(&nlevels, int(1+(legend+(&step/2)-&yieldmin)/&step));
  color=scan(&colors,ncol); output;

  z=legend+&step;
  function='polycont'; output;
  x=&tempmin+(&tempmax-&tempmin)*.05; output;
  z=legend; output;
  end;
run;

data annoall;
   set surf1 surf2 plane1 plane2 legend;
run;

data plotdata;
temp=&tempmin; catalyst=&catmin; yield=&floor; output;
temp=&tempmax; catalyst=&catmax; output;
run;


goptions device=png xpixels=800 ypixels=600;

ODS LISTING CLOSE;
ODS HTML path=odsout body="&name..htm" 
 (title="Catalyst Experiment 3d surface") style=htmlblue;

goptions gunit=pct border cback=white
 htitle=4.5 htext=3 ftitle="albany amt/bold" ftext="albany amt";

title1 ls=1.5 'Catalyst Experiment';

proc g3d data=plotdata;
label catalyst='Catalyst' temp='Temperature' yield='Yield';
scatter catalyst*temp=yield / rotate=40 
 xticknum=5 yticknum=5 zticknum=5
 shape='point' annotate=annoall
 zmin=&floor zmax=&ceil 
 caxis=black
 des='' name="&name";
run;

quit;
ODS HTML CLOSE;
ODS LISTING;
```
Back to [Index](#index)
