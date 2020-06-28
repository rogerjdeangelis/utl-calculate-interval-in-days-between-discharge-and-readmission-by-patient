# utl-calculate-interval-in-days-between-discharge-and-readmission-by-patient
Calculate interval in days between discharge and readmission by patient
    Calculate interval in days between discharge and readmission by patient                                                                                                                    
                                                                                                                                                                                               
         Four Solutions                                                                                                                                                                        
             a. SAS                                                                                                                                                                            
             b. R data table                                                                                                                                                                   
                https://stackoverflow.com/users/13816207/dite-bayu                                                                                                                             
             c. R tidyverse                                                                                                                                                                    
                https://stackoverflow.com/users/3962914/ronak-shah                                                                                                                             
             d. R no packages                                                                                                                                                                  
                https://stackoverflow.com/users/11741943/zhiqiang-wang                                                                                                                         
                                                                                                                                                                                               
         Method (input is patients with admissions)                                                                                                                                            
                                                                                                                                                                                               
             if fist paitient in group set previous discharge date to missing                                                                                                                  
             else set interval between admission and discharge to admission_date - previous discharge_date                                                                                     
                                                                                                                                                                                               
         Notes on the R solutions                                                                                                                                                              
                                                                                                                                                                                               
             1. use haven package to import sas table                                                                                                                                          
             2. note the two key R functons lag and shift                                                                                                                                      
             3. all three R solutions done in one drop down to R                                                                                                                               
             4. To overcome the 8 character column name limit in the SAS v5 transport,                                                                                                         
                the long variable names are stored in the label portion of the V% transport file                                                                                               
             5. You need to use my utl_rens macro to extract the label and                                                                                                                     
                rename the mushed column names creeated by R SASxport package.                                                                                                                 
                You cannot use the SAS table names inside the xport file because a view with the names is used                                                                                 
                in utl_rens macro.                                                                                                                                                             
             6. You also neeed to clear the view if you rerun.                                                                                                                                 
                                                                                                                                                                                               
          Note R can create a V8 SAS xport file according to specs, but SAS cannot import it because                                                                                           
               of a bug in SAS xpt2loc. (plus other issues with formats). A fix is available from                                                                                              
                                                                                                                                                                                               
               https://goo.gl/Adacx9                                                                                                                                                           
               https://github.com/rogerjdeangelis/utl_importing_r_created_v8_transport_files_into_sas_wps/blob/master/utl_importing_r_created_v8_transport_files_into_sas_wps_xpt2loc.sas      
                                                                                                                                                                                               
    github                                                                                                                                                                                     
    https://tinyurl.com/y7otdphd                                                                                                                                                               
    https://github.com/rogerjdeangelis/utl-calculate-interval-in-days-between-discharge-and-readmission-by-patient                                                                             
                                                                                                                                                                                               
    macros                                                                                                                                                                                     
    https://tinyurl.com/y9nfugth                                                                                                                                                               
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                                                                                                 
                                                                                                                                                                                               
    StackOverflow                                                                                                                                                                              
    https://tinyurl.com/y7vzs8fl                                                                                                                                                               
    https://stackoverflow.com/questions/62588034/how-to-calculate-days-of-readmission                                                                                                          
                                                                                                                                                                                               
    /*                   _                                                                                                                                                                     
    (_)_ __  _ __  _   _| |_                                                                                                                                                                   
    | | `_ \| `_ \| | | | __|                                                                                                                                                                  
    | | | | | |_) | |_| | |_                                                                                                                                                                   
    |_|_| |_| .__/ \__,_|\__|                                                                                                                                                                  
            |_|                                                                                                                                                                                
    */                                                                                                                                                                                         
                                                                                                                                                                                               
    options validvarname=upcase;                                                                                                                                                               
    libname sd1 "d:/sd1";                                                                                                                                                                      
    data sd1.have;                                                                                                                                                                             
     format admission_date discharge_date  date10.;                                                                                                                                            
     informat admission_date discharge_date  date10.;                                                                                                                                          
     input id$ admission_date discharge_date ;                                                                                                                                                 
    cards4;                                                                                                                                                                                    
    A 01JAN2017 04JAN2017                                                                                                                                                                      
    A 01FEB2017 05FEB2017                                                                                                                                                                      
    A 01MAY2017 06MAY2017                                                                                                                                                                      
    B 01JAN2017 03JAN2017                                                                                                                                                                      
    B 01MAY2017 09MAY2017                                                                                                                                                                      
    B 01OCT2017 04OCT2017                                                                                                                                                                      
    C 01JAN2017 06JAN2017                                                                                                                                                                      
    C 01FEB2017 01FEB2017                                                                                                                                                                      
    ;;;;                                                                                                                                                                                       
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    /*           _               _                                                                                                                                                             
      ___  _   _| |_ _ __  _   _| |_                                                                                                                                                           
     / _ \| | | | __| `_ \| | | | __|                                                                                                                                                          
    | (_) | |_| | |_| |_) | |_| | |_                                                                                                                                                           
     \___/ \__,_|\__| .__/ \__,_|\__|                                                                                                                                                          
                    |_|                                                                                                                                                                        
    */                                                                                                                                                                                         
                                                                                                                                                                                               
    WANT_R1 total obs=8  (note the long variable names from R)                                                                                                                                 
                                                                                                                                                                                               
            ADMISSION_    DISCHARGE_                                                                                                                                                           
     ID        DATE          DATE      DAYREADMISSION                                                                                                                                          
                                                                                                                                                                                               
     A      01JAN2017     04JAN2017            .                                                                                                                                               
     A      01FEB2017     05FEB2017           28                                                                                                                                               
     A      01MAY2017     06MAY2017           85                                                                                                                                               
     B      01JAN2017     03JAN2017            .                                                                                                                                               
     B      01MAY2017     09MAY2017          118                                                                                                                                               
     B      01OCT2017     04OCT2017          145                                                                                                                                               
     C      01JAN2017     06JAN2017            .                                                                                                                                               
     C      01FEB2017     01FEB2017           26                                                                                                                                               
               _       _   _                                                                                                                                                                   
     ___  ___ | |_   _| |_(_) ___  _ __  ___                                                                                                                                                   
    / __|/ _ \| | | | | __| |/ _ \| `_ \/ __|                                                                                                                                                  
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \                                                                                                                                                  
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/                                                                                                                                                  
     ___  __ _ ___                                                                                                                                                                             
    / __|/ _` / __|                                                                                                                                                                            
    \__ \ (_| \__ \                                                                                                                                                                            
    |___/\__,_|___/                                                                                                                                                                            
    */                                                                                                                                                                                         
                                                                                                                                                                                               
    data want_sas;                                                                                                                                                                             
       set sd1.have;                                                                                                                                                                           
       by id;                                                                                                                                                                                  
       lagdischarge_date = lag(discharge_date);                                                                                                                                                
       if first.id then lagdischarge_date =.;                                                                                                                                                  
       else dayreadmission = admission_date - lagdischarge_date;                                                                                                                               
       drop lagdischarge_date;                                                                                                                                                                 
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    /*                      _                                                                                                                                                                  
     _ __    __ _        __| |                                                                                                                                                                 
    | `__|  / _` |_____ / _` |                                                                                                                                                                 
    | |    | (_| |_____| (_| |                                                                                                                                                                 
    |_|     \__,_|      \__,_|                                                                                                                                                                 
                                                                                                                                                                                               
    */                                                                                                                                                                                         
                                                                                                                                                                                               
                                                                                                                                                                                               
    %utlfkil(d:/xpt/want.xpt);                                                                                                                                                                 
                                                                                                                                                                                               
    %utl_submit_r64(resolve('                                                                                                                                                                  
    library(haven);                                                                                                                                                                            
    library(SASxport);                                                                                                                                                                         
    library(data.table);                                                                                                                                                                       
    library(tidyverse);                                                                                                                                                                        
                                                                                                                                                                                               
    have<-as.data.table(read_sas("d:/sd1/have.sas7bdat"));                                                                                                                                     
                                                                                                                                                                                               
    /* b. data table solution */                                                                                                                                                               
    wantr01<-have[, DAYREADMISSION := as.numeric(ADMISSION_DATE - shift(DISCHARGE_DATE)), ID];                                                                                                 
    for (i in seq_along(wantr01)) {label(wantr01[[i]])<- colnames(wantr01)[[i]]};                                                                                                              
                                                                                                                                                                                               
    /* c. tidyverse solution */                                                                                                                                                                
    wantr02 <- as.data.table(have %>%                                                                                                                                                          
      group_by(ID) %>%                                                                                                                                                                         
      mutate(DAYREADMISSION = ADMISSION_DATE - lag(DISCHARGE_DATE)));                                                                                                                          
    for (i in seq_along(wantr02)) {label(wantr02[[i]])<- colnames(wantr02)[[i]]};                                                                                                              
                                                                                                                                                                                               
    /* d. no packages */                                                                                                                                                                       
    have$DAYREADMISSION <- unlist(lapply(split(have, have$ID),                                                                                                                                 
           FUN=function(x) x$ADMISSION_DATE - lag(x$DISCHARGE_DATE)));                                                                                                                         
    wantr03<-have;                                                                                                                                                                             
    for (i in seq_along(wantr03)) {label(wantr03[[i]])<- colnames(wantr03)[[i]]};                                                                                                              
    write.xport(wantr01, wantr02, wantr03, file="d:/xpt/want.xpt");                                                                                                                            
    '));                                                                                                                                                                                       
                                                                                                                                                                                               
    /* if there is an error and you have to rerun */                                                                                                                                           
    libname xpt clear;                                                                                                                                                                         
    proc datasets lib=work mt=view mt=data;                                                                                                                                                    
     delete __ren001 wantr01  wantr02  wantr03                                                                                                                                                 
                     want_r1 want_r2 want_r3 want_r3 ;                                                                                                                                         
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    libname xpt xport "d:/xpt/want.xpt";                                                                                                                                                       
                                                                                                                                                                                               
    data want_r1; /* need a different name */                                                                                                                                                  
      retain id;                                                                                                                                                                               
      format                                                                                                                                                                                   
         admission_date                                                                                                                                                                        
         discharge_date  date9.;                                                                                                                                                               
                                                                                                                                                                                               
      %utl_rens(xpt.wantr01);                                                                                                                                                                  
      set wantr01;                                                                                                                                                                             
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    data want_r2; /* need a different name */                                                                                                                                                  
      retain id;                                                                                                                                                                               
      format                                                                                                                                                                                   
         admission_date                                                                                                                                                                        
         discharge_date  date9.;                                                                                                                                                               
                                                                                                                                                                                               
      %utl_rens(xpt.wantr02);                                                                                                                                                                  
      set wantr02;                                                                                                                                                                             
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    data want_r3; /* need a different name */                                                                                                                                                  
      retain id;                                                                                                                                                                               
      format                                                                                                                                                                                   
         admission_date                                                                                                                                                                        
         discharge_date  date9.;                                                                                                                                                               
                                                                                                                                                                                               
      %utl_rens(xpt.wantr03);                                                                                                                                                                  
      set wantr03;                                                                                                                                                                             
    run;quit;                                                                                                                                                                                  
                                                                                                                                                                                               
    libname xpt clear;                                                                                                                                                                         
                                                                                                                                                                                               
                                                                                                                                                                                               
