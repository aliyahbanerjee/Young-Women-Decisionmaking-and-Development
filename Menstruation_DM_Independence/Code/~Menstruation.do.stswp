* Written by Aliyah Banerjee
* Last updated 17 July, 2025

* --------------------------------------------------------------------------

* This code studies the effect of age of menstruation on the decisionmaking independence received by young women ages 15-21 in rural households of the Sultanpur and Amethi districts of Uttar Pradesh. 

* The data is sourced from Ahmad & Hazra's "Assessment of knowledge, attitudes, and empowerment of adolescents and young women in Uttar Pradesh" (2020), based on their 2016 cross-sectional study.

* Link to data is: https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/NQYBPM&version=1.1

* ---------------------------------------------------------------------------
clear all

//download and import SPSS data from link
cd "/Users/aliyahbanerjee/Documents/Menstruation_DM_Independence"
import spss using "Data/Datasets/YWSHG_Data.sav"

* In the dataset, variable names correspond to the question numbers in the questionnaire used to survey the households. For more information, it is recommended to download and go through YWSHG_Questionnaire.pdf and YWSHG study_readme file.txt, both available on the Harvard dataset link, to navigate the dataset better.  

//rename variable WM1, which records the age of starting menstruation. 
rename WM1 age_of_menstruation
gen menarche = 1
replace menarche = 0 if age_of_menstruation == 00

//drop observations for young women who have not started menstruating yet, as we are interested in the age of starting menstruation, only for young women who have started. Encoded as byte variable, so although shows "Not yet started", it is value 00 for not yet started. 
drop if age_of_menstruation == 00 

//generate indicator variable to record decisionmaking independence and set to 0. 
gen independent = 0

//replace with 1 if all of WH1, WH2, WH3, WH4, and WH5 have value of either 1 or 5. This means that in matters of marriage, education, spending, etc, the surveyed young woman either took decisions herself or jointly with her parents. 
replace independent = 1 if inlist(WH1,1,5) & inlist(WH2,1,5) & inlist(WH3,1,5) & inlist(WH4,1,5) & inlist(WH5,1,5)

//generate indicator variable to record a more lenient measure of independence and set to 0. 
gen low_independent = 0

//replace with 1 if atleast one of WH1, WH2, WH3, WH4, and WH5 have value of either 1 or 5. This means that in matters of marriage, education, spending, etc, the surveyed young woman either took decisions herself or jointly with her parents in atleast one major matter. 
replace low_independent = 1 if inlist(WH1,1,5) | inlist(WH2,1,5) | inlist(WH3,1,5) | inlist(WH4,1,5) | inlist(WH5,1,5)
tabulate independent menarche, exact
tabulate low_independent menarche, exact
//generate indicator variable to record gender norm attitude scale and set to 0. 
gen gender_norm_attitude_scale = 0

//replace with 1 if out of the questions asked on the questionnaire in the Gender Norm Attitude Scale section in the questionnaire, the young woman gave strictly progressive answers for all questions. Refer to questionnaire for answers represented by each response number. 
replace gender_norm_attitude_scale = 1 if WI1==3 & WI2==3 & WI3==3 & WI4==3 & WI5==3 & WI6==3 & WI7==3 & WI8==3 & WI9==3 & WI10==3 & WI11==3 & WI12==3 & WI13==3 & WI14==3 & WI15A==2 & WI15B==2 & WI15C==2 & WI15D==2 & WI15E==2 & WI15F==2 & WI15G==2

//generate a lenient measure of gender norm attitude scale and set to 0.
gen low_gender_norm_attitude_scale = 0

//replace with 1 if out of the same questions, atleast one answer is strongly indicative of progressive thinking in matters of gender. Refer to questionnaire for answers represented by each response number. 
replace low_gender_norm_attitude_scale = 1 if WI1==3 | WI2==3 & WI3==3 | WI4==3 | WI5==3 | WI6==3 | WI7==3 | WI8==3 | WI9==3 & WI10==3 | WI11==3 | WI12==3 | WI13==3 | WI14==3 | WI15A==2 | WI15B==2 | WI15C==2 | WI15D==2 | WI15E==2 | WI15F==2 | WI15G==2

//generate self efficacy indicator variable and set to 0. 
gen self_efficacy_scale = 0

//replace with 1 if out of the relevant questions in the questionnaire in the Self Efficacy Scale section, the young woman gave progressive answers in all questions. Refer to questionnaire for answers represented by each response number. 
replace self_efficacy_scale = 1 if WJ1 == 1 & WJ2 == 1 & WJ3 == 2 & WJ4 == 2 & WJ5A == 4 & WJ5B == 4 & WJ5C == 4 & WJ5D == 4 & WJ5E == 4 & WJ5F == 4 & WJ5G == 4 & WJ5H == 4 & WJ5I == 4 & WJ5J == 4


gen low_self_efficacy_scale = 0 
replace low_self_efficacy_scale = 1 if WJ1 == 1 | WJ2 == 1 | WJ3 == 2 | WJ4 == 2 | WJ5A == 4 | WJ5B == 4 | WJ5C == 4 | WJ5D == 4 | WJ5E == 4 | WJ5F == 4 | WJ5G == 4 | WJ5H == 4 | WJ5I == 4 | WJ5J == 4
gen SE_GNA = self_efficacy_scale * gender_norm_attitude_scale
count if SE_GNA == 1
probit independent age_of_menstruation SE_GNA i.DIST i.HHTYPE, vce(cluster DIST)
estat gof
margins, dydx(*)
//this above strict reg has lots of issues. Due to very small no. of households with independent = 1 or gender_norm_attitude_scale = 1, gender_norm_attitude_scale ends up predicting independent perfectly and is omitted from the probit regression. Moreover, from counting the interaction variable SE_GNA 's occurences of being 1, there are 0 instances, because both vars have very small no of observations as 1, so there is no observation in which both are 1 and thus the product is always 0 for all observations. 

//thus, we now choose to use the lenient variables in a regression. 
gen low_SE_GNA = low_self_efficacy_scale * low_gender_norm_attitude_scale
count if low_SE_GNA == 1 //confirm that enough observations have 1 for the interaction variable (768 observations), thus obviously the low-intensity individuals vars also have enough observations (if had most as 0, then interaction var would also be 0 in most cases)
probit independent age_of_menstruation low_SE_GNA i.DIST i.HHTYPE, vce(cluster DIST)
estat gof 
margins, dydx(*)

probit low_independent age_of_menstruation low_SE_GNA i.DIST i.HHTYPE, vce(cluster DIST)
estat gof 
margins, dydx(*)

probit independent age_of_menstruation low_gender_norm_attitude_scale low_self_efficacy_scale i.DIST i.HHTYPE, vce(cluster DIST)
estat gof
margins, dydx(*)

probit low_independent age_of_menstruation low_gender_norm_attitude_scale low_self_efficacy_scale i.DIST i.HHTYPE, vce(cluster DIST)
estat gof
margins, dydx(*)

//Fisher tests by category of menarche age
// ages defined from : https://hsph.harvard.edu/news/menstrual-periods-are-arriving-earlier-for-younger-generations-especially-among-racial-minority-and-lower-income-individuals/
recast int age_of_menstruation
gen early_menarche = 0
gen very_early_menarche = 0
gen normal_menarche = 0
gen late_menarche = 0
replace very_early_menarche = 1 if age_of_menstruation < 9
replace early_menarche = 1 if age_of_menstruation >= 9 & age_of_menstruation <11
replace normal_menarche = 1 if age_of_menstruation >= 11 & age_of_menstruation < 16
replace late_menarche = 1 if age_of_menstruation >= 16

//other regressions 
ssc install firthlogit
firthlogit low_gender_norm_attitude_scale menarche
firthlogit progressive menarche


