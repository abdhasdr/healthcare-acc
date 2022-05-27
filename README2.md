---
title: No-Show appointments
description: Reduce late notice cancellations and no-show appointments (disrupted care) to ensure needed care and maximize booking efficiency.

---

# No-Show appointments {: #no-show-appointments }

This accelerator addresses how to minimize no-shows and add confidence to preemptive rebookings.

**AI Impact:** Addressing no-show appointments improves patient outcomes, decreases loss of revenue.

**Prediction details:** Create a model that predicts whether a patient will arrive for an appointment.

**Skill level**: ??

## Business details {: #business-details }

In roughly 5% of scheduled office visits, the patient either does not present or cancels within a window too short for re-booking. Most providers consider this a "no-show." No-show appointments are a subset of a critical health care issue known as _disrupted care_ and cost, on an average, $180 per incident.

!!! tip
    This 5% value _includes_ consideration of automated email reminders.

There are two primary opportunities that this use case addresses:

Issue | Opportunity
----- | -----------
Patient outcome | Patients suffer if they do not get the care they require. Ensuring attendance plays a critical role in patient health.
Revenue loss | A degree of certainty about an open booking slot allows for  preemptive filling by: <br> &bull; Standard over-booking.<br>&bull; Using a "propensity for" model to contact an alternative patient.
Staffing inefficiency | Correct staffing levels improve both patient and employee satisfaction.
Staffing no-shows | In the case of "home health," nurses not arriving when scheduled disrupts patient care more directly.

### Solution value

The following are direct benefits of predicting no-shows:

* Predicting with sufficient lead time may present an opportunity to reach out to the patient to ensure attendance or reschedule the appointment.

* Aggregating predictions at the beginning of the week can assist with staffing and number of appointments that can be booked.

### Qualification criteria

Consider these questions before beginning the project:

* hmmmm
* uhhhhh
* welllll

## Technical details {: #technical-details }

I DROPPED THIS BECAUSE YOU DON'T SEEM TO MENTION IT AGAIN: `2. Fill open appointments (i.e., overbooking). IF THEY ARE OPEN IT IS NOT OVERBOOKING. ALSO WHY DOES "FILL OPEM APPOINTMENTS" NEED A SHORT TIME WINDOW? IS IT ADDRESSED BELOW?`


The main consumption model you will build for no-show appointments is to create a prioritizes call-list the week prior to appointments. This model requires a fairly short time window. If you predict no-shows for six months from now, for example, it doesn’t make sense to send reminders. One week is standard standard and provides enough time to contact a patient or re-book.

Consumption of the model is relatively simple. You can process all of next week’s appointments on Friday morning to create a prioritized list. The call list can be broken out by clinic and distributed to stakeholders. [Prediction Explanations](pred-explain/index) may be helpful but aren’t always required since, generally, the efficacy of reaching out to a patient with a reminder will not improve if more information is given.

!!! tip
	This process is to determine no-shows. If you want insights (for example, why is the no-show rate for one clinic or provider significantly higher than others?), DO THIS INSTEAD.

### How does DataRobot help? {: #how-does-datarobot-help }

DataRobot automates the following tasks in this use case: DOES THE USE CASE ACTUALLY USE ALL THESE?

* Connects to your data and determines which data sources are relevant and visualizes the contribution to accuracy and the relationship between each column and no-show. [LINK TO HOW/WHAT]

* Aggregates predictors such as historical patient no-show rate. [LINK TO HOW/WHAT]

* Performs NLP on text data (for example, booking notes) then visualizes how snippets of text may affect no-shows. [LINK TO HOW/WHAT]

* Trains dozens of potential solutions and helps you optimize the cost of a bad prediction vs. the value of a good one. IS THIS PROFIT CURVE?

* Recommends a solution where you can apply [bias mitigation](bias-mitigation), for example for race or gender. WE CANNOT GUARANTEE ANYTHING...I CHANGED THIS BUT WANT TO ENSURE IT DOESN'T COME BACK WITH THE WORD GUARANTEE.

* Generates [explanations](pred-explain/index) that contribute to each score. YOU SAID ABOVE THESE WEREN'T NEEDED 

* Documents that your solution treats protected classes fairly with a built-in bias and fairness assessment. DOCUMENTS IN WHAT WAY? COMPLIANCE REPORT? VISUALIZATIONS?

* Prepares the model to make predictions that integrate with your BI tools or to generate a prioritized call-list that optimizes your contacts. I DONT UNDERSTAND THIS

* Tracks the health of your model (including bias and other [model monitoring](monitor/index)) and notifies you whenever a potential issue needs to be addressed

## Data prep {: #data-prep }

For best results, your dataset should cover two years of historical appointments with `show/no-show` as the target. This time window will allow inclusion of both seasonality and some before/after COVID activity. 

!!! tip
	Engineer historical no-show rates at the patient, physician, and office/clinic level. Use a description of the visit, rather than a numeric code, for more interpretable [word clouds](word-cloud).

The shape of the data should be at the visit level (one row per visit) and must include:

* Patient ID
* Date/time of the scheduled appointment
* Date the appointment was made
* Appointment location
* Physician seen
* Target (`show/no-show`)

The following additional data, if available, is beneficial to produce the most accurate model: SOURCES? DO YOU MEAN ADDITIONAL DATA?

* Demographics, including age and gender, occupational status, and site location. (When a hospital is concentrated in one area, the effect of geography is muted.) DONT UNDERSTAND THE PARENTHETICAL. "A HOSPITAL" IS ALWAYS CONCENTRATED, NO? 

* If the patient is a minor, information about the responsible custodian. SUCH AS?

* Patient history, such as comorbidities, procedures, medications, etc.

* Weather data, as it can affect travel between home and clinic if extreme conditions exist. This one is fun to include but unlikely to help. If you do include it, watch for leakage&mdash;use the forecasted weather, not actuals UMMMMM, DO WE WANT TO TELL THEM THIS?

* County COVID data (if available). The number of cases may affect certain types of visits, including vaccinations. Hopefully we can phase this out soon.

#### Sample schema {: #sample-schema }

A sample schema used with a customer is below:

| **Column Name**         | Type        | **Description**                                              |
| ----------------------- | ----------- | ------------------------------------------------------------ |
| DNA (did not arrive)    | Binary      | Binary target variable (0, 1)                                |
| Site                    | Categorical | site name                                                    |
| ClinicName              | Text        | name of clinic                                               |
| ClinicType              | Categorical | e.g. Trauma, Pre-op, Regular                                 |
| Hour                    | Numerical   | Hour of appointment                                          |
| Day                     | Numerical   | Day of appointment                                           |
| Month                   | Numerical   | Month of appointment                                         |
| Duration                | Numerical   | Duration of appointment                                      |
| LeadTime                | Numerical   | Time, in days, between the date the appointment was created and when it occurred |
| AttendType              | Categorical | e.g. New, Follow-Up                                          |
| Priority                | Numerical   | Appointment Priority                                         |
| Specialty               | Categorical | Appointment speciality                                       |
| IsCancerRef             | Binary      | Whether the appointment is related to a cancer referral      |
| Attendance              | Numerical   | Numerical variable, between 0 and 1, which is the patients past attended appointment divided by their past total appointments |
| PreviousDNA             | Boolean     | Binary indicator of whether the patient has ever had a DNA   |
| Sex                     | Categorical | Patients sex                                                 |
| Age                     | Numerical   | Patients age                                                 |
| IncomeDecile            | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| CrimeDecile             | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| EmploymentDecile        | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| EducationDecile         | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| HealthDecile            | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| HousingDecile           | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| EnvironmentDecile       | Numerical   | ONS deprivation deciles based on the patient’s postcode      |
| RoadMiles               | Numerical   | Miles from the patients postcode to the site of the appointment |
| LiverDisease            | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| MI                      | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| Dementia                | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| PepticUlcer             | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| CongestiveHeartFalure   | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| ConnectiveTissueDisease | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| CrohnsDisease           | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| Diabetes                | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| Hemi/Paraplegiac        | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| PulmonaryDisease        | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| HIV/AIDS                | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| VascularDisease         | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| Cancer                  | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| RenalDisease            | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| RheumaticDisease        | Binary      | Indicator variable for various conditions that the patient has been previously diagnosed with |
| ConditionCount          | Numerical   | Count of special conditions                                  |

#### Sample data {: #sample-data }

The following shows sample data for the above schema:

| DNA  | SITE | ClinicName                           | ClinicType | Hour | Day  | Month | Duration | LeadTime | ATTEND_TYPE | Priority | Specialty                                   | IsCancerRef | Attendance | PreviousDNA | Sex    | ActualAge | IncomeDecile | CrimeDecile | EmploymentDecile | EducationDecile | HealthDecile | HousingDecile | EnvironmentDecile | RoadMiles | Liver Disease | MI   | Dementia | Peptic Ulcer | Congestive Heart Failure | Connective Tissue Disorder | Crohn's Disease | Diabetes | Hemi/Paraplegia | Pulmonary Disease | HIV/AIDS | Vascular Disease | Cancer | Renal Disease | Rheumatic Disease | ConditionCount |
| ---- | ---- | ------------------------------------ | ---------- | ---- | ---- | ----- | -------- | -------- | ----------- | -------- | ------------------------------------------- | ----------- | ---------- | ----------- | ------ | --------- | ------------ | ----------- | ---------------- | --------------- | ------------ | ------------- | ----------------- | --------- | ------------- | ---- | -------- | ------------ | ------------------------ | -------------------------- | --------------- | -------- | --------------- | ----------------- | -------- | ---------------- | ------ | ------------- | ----------------- | -------------- |
| 0    | FGH  | mr b frank fu ortho                  | Regular    | 10   | 2    | 1     | 25       | 97       | CC_FOLLOWUP | 3        | TRAUMA AND ORTHOPAEDICS                     | 0           | 0.92       | 1           | Female | 72        | 5            | 9           | 4                | 5               | 2            | 10            | 1                 | 3.7       | 0             | 1    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 1                | 0      | 0             | 0                 | 2              |
| 0    | RLI  | dr d kim oncology  day hosp          | Regular    | 14   | 4    | 1     | 40       | 28       | CC_FOLLOWUP | 3        | CLINICAL ONCOLOGY (previously RADIOTHERAPY) | 0           | 0.98       | 1           | Male   | 63        | 8.142857143  | 6.07254     | 7.428571429      | 9.142857143     | 4.928571429  | 6.142857143   | 3.357142857       | 1.4       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | RLI  | dr f s htke haematology              | Regular    | 9    | 4    | 1     | 15       | 28       | CC_FOLLOWUP | 3        | CLINICAL HAEMATOLOGY                        | 0           | 0.97       | 0           | Male   | 74        | 6.2          | 4.7         | 6.1              | 6.5             | 3.4          | 8.7           | 4.3               | 4.6       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 1      | 0             | 0                 | 1              |
| 0    | WGH  | dr charleston sat geriatric med  wgh | Regular    | 9    | 6    | 3     | 20       | 36       | CC_FOLLOWUP | 3        | GERIATRIC MEDICINE                          | 0           | 1          | 1           | Female | 92        | 5            | 10          | 8                | 8               | 7            | 4             | 3                 | 12.8      | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | FGH  | dr jones oncology fri fgh            | Regular    | 16   | 2    | 2     | 20       | 182      | CC_FOLLOWUP | 3        | CLINICAL ONCOLOGY (previously RADIOTHERAPY) | 0           | 0.94       | 0           | Female | 55        | 2            | 1           | 1                | 2               | 1            | 8             | 1                 | 2.4       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 1      | 0             | 0                 | 1              |
| 0    | FGH  | dr marsden monday haematology  fgh   | Regular    | 12   | 3    | 1     | 30       | 182      | CC_FOLLOWUP | 3        | CLINICAL HAEMATOLOGY                        | 0           | 1          | 1           | Male   | 84        | 8            | 10          | 7                | 6               | 7            | 5             | 3                 | 25.3      | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 1        | 0               | 0                 | 0        | 0                | 1      | 0             | 0                 | 2              |
| 1    | FGH  | orthotic thursday  fgh               | Regular    | 10   | 4    | 1     | 10       | 197      | CC_FOLLOWUP | 3        | REHABILITATION                              | 0           | 0.86       | 1           | Male   | 17        | 3            | 9           | 4                | 4               | 1            | 10            | 7                 | 2.2       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | FGH  | mr k jones antenatal                 | Regular    | 13   | 3    | 1     | 15       | 350      | CC_FOLLOWUP | 3        | OBSTETRICS                                  | 0           | 0.76       | 1           | Female | 30        | 1            | 1           | 2                | 1               | 1            | 8             | 2                 | 2.7       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | RLI  | visual fields  qvh                   | Regular    | 11   | 3    | 1     | 15       | 91       | CC_FOLLOWUP | 3        | CLINICAL PHYSIOLOGY                         | 0           | 0.67       | 1           | Female | 82        | 7.6          | 8.4         | 7.6              | 7.6             | 5.3          | 5.7           | 4.2               | 9.3       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 1                | 0      | 0             | 0                 | 1              |
| 0    | FGH  | mr johnston ortho  fgh               | Regular    | 13   | 1    | 1     | 15       | 56       | CC_NEW      | 2        | TRAUMA AND ORTHOPAEDICS                     | 0           | 0.88       | 1           | Female | 86        | 4            | 7           | 2                | 3               | 2            | 10            | 1                 | 1.4       | 1             | 0    | 0        | 0            | 0                        | 1                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 1                 | 3              |
| 0    | RLI  | mr bolton ophthalmology  rli         | Regular    | 15   | 3    | 1     | 15       | 105      | CC_NEW      | 3        | OPHTHALMOLOGY                               | 0           | 0.78       | 1           | Female | 44        | 7.6          | 8.6         | 7.3              | 7.2             | 5.3          | 5.7           | 4.2               | 9.2       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 1    | WGH  | mr wilson colorectal surgery         | Regular    | 13   | 1    | 1     | 15       | 31       | CC_NEW      | 3        | COLORECTAL SURGERY                          | 0           | 0.76       | 1           | Male   | 27        | 7            | 8           | 6                | 4               | 6            | 10            | 4                 | 16.3      | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | RLI  | l waters exercise tolerance testing  | Regular    | 14   | 1    | 2     | 10       | 31       | CC_FOLLOWUP | 2        | CLINICAL PHYSIOLOGY                         | 0           | 0.57       | 1           | Male   | 59        | 4.615384615  | 5.36809     | 4.076923077      | 4.769230769     | 4.54228      | 8.538461538   | 5.12456           | 4.7       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | RLI  | dr f s htke haematology              | Regular    | 10   | 1    | 8     | 15       | 24       | CC_FOLLOWUP | 3        | CLINICAL HAEMATOLOGY                        | 0           | 1          | 1           | Male   | 65        | 9            | 9.5         | 8.9              | 8.75            | 7.8          | 6.75          | 6                 | 16.2      | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 1      | 0             | 0                 | 1              |
| 0    | WGH  | lucy scott parkinson nurse  wgh      | Regular    | 9    | 1    | 2     | 15       | 41       | CC_FOLLOWUP | 3        | GERIATRIC MEDICINE                          | 0           | 0.98       | 0           | Male   | 74        | 7.571428571  | 6.845218    | 7.571428571      | 7.142857143     | 6.857142857  | 6.142857143   | 3.142857143       | 2.4       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | WGH  | dr l khan haem                       | Regular    | 9    | 4    | 1     | 20       | 22       | CC_FOLLOWUP | 3        | CLINICAL HAEMATOLOGY                        | 0           | 0.94       | 0           | Female | 32        | 7.25         | 58          | 7.25             | 8.2             | 6.25         | 7.8           | 2.25              | 2.8       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | WGH  | helen harris lymphodema nurse        | Regular    | 15   | 3    | 3     | 10       | 182      | CC_FOLLOWUP | 3        | MEDICAL ONCOLOGY                            | 0           | 1          | 1           | Female | 68        | 8.5          | 8.625       | 8.35             | 7.75            | 7.538        | 7.375         | 5.125             | 0.6       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 1      | 0             | 0                 | 1              |
| 0    | WGH  | dr charleston fri geriatric med  wgh | Regular    | 9    | 5    | 1     | 20       | 189      | CC_FOLLOWUP | 3        | GERIATRIC MEDICINE                          | 0           | 0.54       | 1           | Female | 84        | 7            | 8           | 7                | 4               | 6            | 10            | 2                 | 16.3      | 0             | 0    | 0        | 1            | 1                        | 0                          | 1               | 0        | 0               | 0                 | 0        | 0                | 0      | 1             | 0                 | 4              |
| 0    | RLI  | f greenhalgh dietitian  rli          | Regular    | 10   | 1    | 1     | 20       | 126      | CC_FOLLOWUP | 3        | DIETETICS                                   | 0           | 0.67       | 1           | Female | 4         | 4.75         | 2.85        | 4.25             | 4.35            | 3            | 6.88          | 4                 | 2.4       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |
| 0    | RLI  | f greenhalgh dietitian  rli          | Regular    | 9    | 2    | 1     | 20       | 96       | CC_FOLLOWUP | 3        | DIETETICS                                   | 0           | 0.64       | 1           | Female | 2         | 5.25         | 2.75        | 4.85             | 4.75            | 3            | 7.166666667   | 3.75              | 4.8       | 0             | 0    | 0        | 0            | 0                        | 0                          | 0               | 0        | 0               | 0                 | 0        | 0                | 0      | 0             | 0                 | 0              |

## Modeling and insights {: #modeling-and-insights }

The following sections provide some consideration to keep in mind for modeling.

### Recommended settings {: #recommended-settings }

For this use case, consider the following for model building:

- Run the project as [OTV](otv) so that the model is optimized on the most recent data (and you have a built-in holdout). If you are not able to run OTV, use an entire year with [group partitioning](partitioning#group-partitioning-group) on patient ID. WHY WOULDN'T THEY BE ABLE TO?

- Aggregation (rather than feature discovery) is more likely to yield easily interpretable findings. DEFINE AGGREGATION. IN PAXATA? DONT THINK WE SHOULD RECOMMEND A PROCESS THAT MAY NOT BE AVAILABLE OR COSTS

- Consider experimenting with 30/90/180-day windows since one of the key features is historical no-show rate. THIS TIME WINDOW INSTEAD OF THE 1 YEAR YOU RECOMMENDED ABOVE?

- Use caution when generating historical no-show rates. Should be based on appointment date to avoid leakage. THIS DOESN'T APPEAR TO BE A SETTING--WHAT SHOULD BE BASED ON APPOINTMENT DATE? IF TARGET IS SHOW/NO, WHAT IS THE "BASIS" FIELD?

- For all other settings, you can use the default values (for example, validation, optimization metric, etc.)

DON'T YOU WANT TO TELL THEM TO SET UP BIAS MITIGATION?

### Key insights {: #key-insights }

Some of the key general insights from this use case are:

- The more time that has passed between the date-of-scheduling and the appointment date, the higher the no-show rate.

- Patients who were late in the past are more likely to show up in the future (i.e., historical no-show rate). It’s likely that historical no-show rates at the clinic, office, or physician level will also yield interesting insights. WHAT FEATURE IS MEASURING WHETHER THEY WERE LATE VS A NO SHOW? NOT SURE HOW THESE SENTENCES QUITE TIE TOGETHER EITHER

- A combination of age/visit type can yield interesting insights&mdash;children always show up because parents care for their children more than they care for themselves. 

- Depending on your clientele, minor or routine visits (for example, flu shots, mammograms) have lower no-show rates. 

*Bias could be a factor and should be investigated.*

### Model interpretation {: #model-interpretation }

The following are examples from a completed use case, meant for illustration only. You can expect _similar_ insights but your data will dictate.

IS IT OK TO SHOW CUSTOMER DATA? I THINK NO...

Use [Feature Impact](feature-impact) to visualize, at a high level, which features are driving model decisions the most. 

![](images/sa-noshow-impact.png)

The following shows an example of [Feature Effects](feature-effects) for _days since scheduled_ (target = `no-show`)

THIS DOESN'T LOOK LIKE FEATURE EFFECTS...IT LOOKS SORT OF LIKE THE HISTOGRAM

![](images/sa-noshow-effects.png)

The following shows an example of [Feature Effects](feature-effects) for "Historical no-show rate" (target = `no-show`)

THIS _DOES_ LOOK LIKE FEATURE EFFECTS...MENTION PARTIAL DEPENDENCE AND WHY THAT MATTERS AND ALSO THERE IS NOTHING IN HERE CALLED "Historical no-show rate"


![](images/sa-no-show-nos.png)

The following show a word cloud based on appointment type: AND TELLS YOU WHAT? TIE THIS BACK TO THE USE CASE

![](images/sa-noshow-wordcloud.png)


## Results OF WHAT? WEREN'T THE VISUALIZATION RESULTS?

Logloss or AUC can be deceptive metrics. YOU SAID TO USE THE DEFAULT METRICS. You'll want to look at the confusion matrix to estimate ROI. NOT THE PROFIT CURVE? ALSO, WHY ISN'T INCLUDED WITH THE VIZ ABOVE IF THEY WANT TO LOOK AT IT.

However, simply using the raw prediction works for rank-ordering the call list. CAN YOU SHOW US A RESULT OF THAT PREDICTION?

### Further analysis {: #further-analysis }

Based on average industry values, you can complete [Profit Curve](profit-curve) making the following assumptions:

- Missed revenue of a no-show appointment: $180 
- Avgerage cost of a (non-automated) phone call reminder: $4
- TP and FN values: $0 (because that represents the status quo)

Maximizing profit provides estimated ROI and a threshold. It's useful to assume a conservative turnaround rate (e.g., only 30% of patients reminded  will change their behavior).

A THRESHOLD FOR WHAT/WHERE IS THAT APPLIED. WHERE DO YOU APPLY THAT 30%?

Operational constraints are also a factor. It's unlikely that a doctor's office has time to reach out to every patient with a reminder, nor overbook 100% of visits. 

## Model serving {: #model-serving }

Deploy your model after setting the threshold for maximum ROI. WHERE DO THEY DO THIS? DO YOU MEAN PREDICTION THRESHOLD?

- In setting up the model for use, it's important to automate the features (either the aggregation or secondary files for discovery) HOW DO THEY DO THIS?

- If the file WHAT FILE is prepared DEFINE PREPARED you can use the drag-and-drop in the UI or the API for predictions. WHICH API APPROACH ARE YOU RECOMMENDING?

WHAT ARE THESE OPTIONS OF/FOR? IS THIS LIKE "HOW TO NOW USE YOUR MODEL"?

- Option 1: Using a CSV file is fine for a call-list, but phone numbers are looked up by the dialer or linked beforehand.THIS IMPACTS WHAT? It's most likely the list will be stratified and distributed by clinic or office (because each is responsible for its own patients). NOT CLEAR WHAT THE POINT IS HERE

- Option 2: You can create a dashboard or [an AI app](app-builder/index) that allows browsing the  number of predicted no-shows by office. Those vacancies can then be filled either by FIFO or a cultivated list of recommended patients (supplied by another model).

## Monitoring and management {: #monitoring-and-management }

It is important to [monitor](monitor/index) your model's predictive performance. Specifically:

* Monitor [data drift](data-drift) because there are seasonal changes as well as changes in overall public health. (This has shown especially true during the recent COVID pandemic).

	!!! note
		If you use day-of-week or month in your model (not completely without value) then your drift will always start off as a red alert (and will stay that way until the model has seen enough data). WHAT MIGHT YOU USE IF NOT DAY OR MONTH?

* Keep an eye on the aggregated features. HOW, AND WHAT ARE THESE AND DID WE MAKE THEM OURSELVES. They're impactful and also the most likely place for error (or delay in updating). 

* Monitoring [accuracy](deploy-accuracy) is a viable approach since you should have actuals arriving every day.

* [Fairness monitoring](mlops-fairness), while not a requirement, can provide interesting insights.
