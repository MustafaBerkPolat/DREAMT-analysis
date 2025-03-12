# DREAMT Measurement, Health Indicator and Condition Correlation Analysis

## Overview

DREAMT is a dataset collected from 100 participants, including high-resolution signals from an Empatica E4 smartwatch designed to measure data, expert sleep technician-annotated sleep stage labels and clinical metadata related to sleep health and disorder. This analysis is an exploration of the data within this set, and how measurements and medical health conditions correlate to one another.


## Takeaways


## Data Used

Only a single dataset was used for this analysis:
 - [DREAMT: Dataset for Real-time sleep stage EstimAtion using Multisensor wearable Technology](https://physionet.org/content/dreamt/2.0.0/)

The data for the measurements taken during sleep are as follows:

Wearable E4 Signals

    TIMESTAMP [s] (64 Hz): The timestamp shifted and started with 0, with a frequency of 64 Hz.
    BVP(64 Hz): Blood volume pulse derived from the photoplethysmography (PPG) sensor.
    IBI[ms]: Inter-beat interval is the time interval between individual beats of the heart, derived from the photoplethysmography (PPG) sensor.
    EDA [μS] (4 Hz): Electrodermal activity from the galvanic skin response sensor.
    TEMP [°C] (4 Hz): Skin temperature from the infrared thermopile sensor.
    ACC [1/64g] (32 Hz): Triaxial accelerometry with each axis named ACC_X, ACC_Y, and ACC_Z.
    HR [bpm] (1 Hz): Heart rate is estimated from the BVP signal.

Sleep labels:

    Sleep_Stage: The technician-annotated sleep labels derived from PSG are recorded every 30 seconds.
        P: Preparation stage, stages before the PSG recording starts
            In data_100Hz, preparation stage (P) is labeled as wake stage (W)
        W: Wake stage
        N1: Non-rapid eye-movement (NREM) stage 1
        N2: Non-rapid eye-movement (NREM) stage 2
        N3: Non-rapid eye-movement (NREM) stage 3
        R: Rapid eye movement (REM)
        Missing: No sleep stage labeled on PSG
            The occurrence of the "Missing" label is extremely low. Significant missingness has been found in only 2 participants, who had their PSG re-setup during the overnight study, which resulted in 15 minutes of consecutive missing labels each. We also found one epoch with "Missing" label each in 4 other participants.

Polysomnography (PSG) Signals

    EEG Channels [μV]:
        C4-M1, F4-M1, O2-M1, T3-CZ, CZ-T4: Channels recording brain activity from different scalp locations, providing insights into sleep stages and cortical activity.
    EOG Channels [μV]:
        E1, E2: Channels monitoring eye movements, essential for identifying rapid eye movement (REM) sleep.
    EMG Channel [μV]:
        CHIN: Monitoring chin muscle activity to detect muscle tone changes during sleep.
    ECG Channel [μV]:
        ECG: Recording the heart's electrical activity, essential for detecting heart rate and rhythm variations.
    Respiratory Channels [μV]:
        PTAF: Airflow monitored by pressure transducer.
        FLOW: Airflow monitored by temperature changes.
        THORAX, ABDOMEN: Channels monitoring respiratory effort through chest and abdominal movements.
    Snoring [μV]:
        SNORE: Channel monitoring snoring sounds.
    Leg Movement Channels [μV]:
        LAT, RAT: Monitoring left and right anterior tibialis muscle activity for detecting periodic leg movements.
    Blood Oxygen Saturation [%]:
        SAO2: Monitoring the percentage of oxygen in the blood.
        
More information (including participant medical conditions) is available in the metadata file within the dataset.

## Data Processing and Analysis

### Data Cleaning

The datasets used in this analysis were the 100Hz measurements data, and the participant info data. The 100Hz measurements data contain the above information, spanning over 3 million rows for each participant and over 3 billion rows total, with each row tracking 1/64th of a second. The participant info data contains age, gender, body mass info, apnea-hypopnea index, medical history and observed sleep disorders of participants. The medical history and sleep disorder information is stored as comma-separated conditions as strings under one column for each category, and was not consistently formatted with certain conditions being written under different aliases for different participants. A function had to be defined to separate these string columns into separate boolean columns, and manual work needed to be done to identify the inconsistently named conditions and merge them under one condition. All variations of teeth grinding were combined under bruxism.

For the measurements data, aggregation functions were used to calculate statistical values of each data for each participant. The mean, standard deviation, minimum and maximum values for each of the variable measurements were calculated, while the Obstructive_Apnea, Central_Apnea and Hypnopnea data were aggregated with mean, standard deviation and sum functions as they track whether the related events were observed during a given moment or not, and only took 0 or 1 as values within the dataset. Additionally, the data was grouped based on sleep stages and the duration of each unique instance of a sleep stage was calculated from the timestamp data, allowing the calculation of the mean, standard deviation, minimum and maximum durations of each kind of sleep stage for analysis.


### Visualization

The visualization for this project is done entirely within Python using Matplotlib and Seaborn, with the primary focus being on correlation heatmaps

![Medical Disorder History by Occurence in Participants](https://github.com/user-attachments/assets/ab743fc0-3ef9-4052-9f3c-46dcb8eb35dc)

![Sleep Disorders by Occurence in Participants](https://github.com/user-attachments/assets/89b36a5a-f9b2-467f-8406-c0d5b30b532f)

For the most part, both the observed and the reported disorders were more common in female participants. There were a total of 55 female participants and 45 male participants. Sleep apnea and asthma were the exception, as they were more common in men despite men being under-represented in this research.


![Box Plot AHI Analysis for Observed Sleep Disorders](https://github.com/user-attachments/assets/e7a6cba5-a64c-49d8-8ce6-e82bf6a4cc84)

Box plot analysis showcases the high amount of outliers in this dataset, which can be attributed to the low participant count. Given that apnea-hypopnea index measures the count of apnea events during sleep, it being lower for people with sleep-interrupting or insomnia-adjacent conditions like bruxism and chronic fatigue are expected, but some of the conditions like hypersomnia and REM sleep behavior disorder were observed in only a single participant, and those with high observation rates like sleep apnea (52 participants out of 100) or excessive daytime sleepiness (34 participants out of 100) have much milder differences between the AHIs of those that have them versus those who don't. 


![Box Plot AHI Analysis for Medical History Conditions](https://github.com/user-attachments/assets/9b76336e-df22-4b9e-99c0-a8b9e0175389)



## Citations

Original Paper:

Wang, W.K., Yang, J., Hershkovich, L., Jeong, H., Chen, B., Singh, K., Roghanizad, A.R., Shandhi, M.M.H., Spector, A.R. &amp; Dunn, J.. (2024). Addressing Wearable Sleep Tracking Inequity: A New Dataset and Novel Methods for a Population with Sleep Disorders. <i>Proceedings of the fifth Conference on Health, Inference, and Learning</i>, in <i>Proceedings of Machine Learning Research</i> 248:380-396 Available from https://proceedings.mlr.press/v248/wang24a.html.

Resource:

Wang, K., Yang, J., Shetty, A., & Dunn, J. (2025). DREAMT: Dataset for Real-time sleep stage EstimAtion using Multisensor wearable Technology (version 2.0.0). PhysioNet. https://doi.org/10.13026/0vrv-nn81

PhysioNet:

Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. Circulation [Online]. 101 (23), pp. e215–e220
