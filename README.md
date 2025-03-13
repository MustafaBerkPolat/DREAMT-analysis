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


### Visualizations

The visualization for this project is done entirely within Python using Matplotlib and Seaborn, with the primary focus being on correlation heatmaps

![Medical Disorder History by Occurence in Participants](https://github.com/user-attachments/assets/ab743fc0-3ef9-4052-9f3c-46dcb8eb35dc)

![Sleep Disorders by Occurence in Participants](https://github.com/user-attachments/assets/89b36a5a-f9b2-467f-8406-c0d5b30b532f)

For the most part, both the observed and the reported disorders were more common in female participants. There were a total of 55 female participants and 45 male participants. Sleep apnea and asthma were the exception, as they were more common in men despite men being under-represented in this research.


### Box Plot AHI Analysis for Observed Sleep Disorders
![Box Plot AHI Analysis for Observed Sleep Disorders](https://github.com/user-attachments/assets/e7a6cba5-a64c-49d8-8ce6-e82bf6a4cc84)

Box plot analysis showcases the high amount of outliers in this dataset, which can be attributed to the low participant count. Given that apnea-hypopnea index measures the count of apnea events during sleep, it being lower for people with sleep-interrupting or insomnia-adjacent conditions like bruxism and chronic fatigue are expected, but some of the conditions like hypersomnia and REM sleep behavior disorder were observed in only a single participant, and those with high observation rates like sleep apnea (52 participants out of 100) or excessive daytime sleepiness (34 participants out of 100) have much milder differences between the AHIs of those that have them versus those who don't. 


### Box Plot AHI Analysis for Medical History Conditions
![Box Plot AHI Analysis for Medical History Conditions](https://github.com/user-attachments/assets/9b76336e-df22-4b9e-99c0-a8b9e0175389)

The medical history data for participants is more reliable, as these conditions are represented at a higher ratio compared to the observed conditions. Participants with asthma, depression and anxiety have lower AHIs, possibly due to lower quality of sleep causing apnea events to interrupt their sleep, but such a commentary cannot be made with this data alone as participants with these conditions are not any more likely to wake up during the night according to the measurements taken, based on the duration and occurrence count of the different sleep stages. The most noteworthy finding here is that those with sleep apnea diagnoses do not have higher AHI score than those who do not, indicating that this condition may be under-diagnosed compared to its actual occurrence. 


### Correlation Heatmap for REM Sleep Data
![Correlation Heatmap for REM Sleep Data](https://github.com/user-attachments/assets/45284393-d4a0-4e75-add8-25ae346ddb03)

The REM sleep measurements have several correlations that are either expected or inconsequential. A higher AHI score is associated with higher occurrences of obstructive apnea events during sleep, with roughly 0.6 correlation. The airflow measured by temperature changes (FLOW) has a 0.8 correlation with central apnea occurrences, most likely due to the extended periods of slow breating increasing the temperature of the exhaled breath through ambient body temperature as this measurement is made by an oronasal thermal sensor according to the [research paper](https://raw.githubusercontent.com/mlresearch/v248/main/assets/wang24a/wang24a.pdf) itself. 

Only one participant is reported to have rem sleep behavior disorder (RBD), so any correlations with this condition would most likely be unreliable due to low sample size and lack of a decent control group. Interestingly, all brain activity and only right anterior tibialis muscle activity (RAT) are very positively correlated with one another (>0.9), with the exception of T3-CZ measurements which corresponds to the left temporal region of the brain, responsible for verbal information processing and memory formation. 

As the C4-M1, F4-M1, O2-M1 and CZ-T4 electrode locations during an EEG exam correspond to the motor cortex, right frontal lobe (which helps regulate muscle movements), the right occipital region (which processes visual information) and the right temporal region (which handles non-verbal information processing) respectively, their high activity alongside high RAT implies dream activity occurring. The negative correlation with T3-CZ is potentially due to the cyclical nature of dreaming and then the dream being committed to memory by the left temporal region while the other regions rest, but the lack of correlation with left leg activity is something to explore in a deeper research. 

E1 and E2, which are eye activities measure by an electrooculogram, also have a high correlation with electrode measurements other than T3-CZ, also most likely due to dream activity


### Correlation Heatmap for Awake Data
![Correlation Heatmap for Awake Data](https://github.com/user-attachments/assets/52975066-a8f5-4ac4-8fa1-219405491b79)

The main noteworthy correlation here is the awake stage duration and the slightly negative correlation it has with central apnea occurrence (-0.15), hypnopnea occurrence (-0.30) and obstructive apnea occurrence (-0.15), meaning that people who experience different variations of apnea spend less time awake in bed. This may be due to these people having less trouble falling asleep when they initially get in bed. Most other correlations are weak positive correlations between AHI/OAHI and the different apnea variations, which is expected. 


### Correlation Heatmap for Non-REM Sleep Data
![Correlation Heatmap for Non-REM Sleep Data](https://github.com/user-attachments/assets/58fd2d8a-122e-4517-a9a5-5fc2343e9bc0)


## Citations

Original Paper:

Wang, W.K., Yang, J., Hershkovich, L., Jeong, H., Chen, B., Singh, K., Roghanizad, A.R., Shandhi, M.M.H., Spector, A.R. &amp; Dunn, J.. (2024). Addressing Wearable Sleep Tracking Inequity: A New Dataset and Novel Methods for a Population with Sleep Disorders. <i>Proceedings of the fifth Conference on Health, Inference, and Learning</i>, in <i>Proceedings of Machine Learning Research</i> 248:380-396 Available from https://proceedings.mlr.press/v248/wang24a.html.

Resource:

Wang, K., Yang, J., Shetty, A., & Dunn, J. (2025). DREAMT: Dataset for Real-time sleep stage EstimAtion using Multisensor wearable Technology (version 2.0.0). PhysioNet. https://doi.org/10.13026/0vrv-nn81

PhysioNet:

Goldberger, A., Amaral, L., Glass, L., Hausdorff, J., Ivanov, P. C., Mark, R., ... & Stanley, H. E. (2000). PhysioBank, PhysioToolkit, and PhysioNet: Components of a new research resource for complex physiologic signals. Circulation [Online]. 101 (23), pp. e215–e220
