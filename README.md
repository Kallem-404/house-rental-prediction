# house-rental-prediction
Import libraries
# import libraries
%matplotlib inline
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt 
import seaborn as sns
Load data
# load data into dataframe 
rental = pd.read_csv("RentRaw.csv", index_col=0, low_memory=False)
rental.head()
BuiltUpSize	City	CreatedDateTime	CreatedUser	DataSourceID	Furnishing	Latitude	Longitude	NoOfBathroom	NoOfBedroom	...	Postcode	PostedDate	PropertyAddress	PropertyName	PropertyType	RentalPerMth	SourceUrl	State	UpdatedDateTime	UpdatedUser
ID																					
1	1000	Bandar Utama	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.14195	101.616	2	3	...	47400	2018-07-30 00:00:00	Jalan Masjid	Pelangi Utama	Condominium	2200	https://www.iproperty.com.my/property/bandar-u...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
2	1000	Bandar Utama	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.14195	101.616	2	3	...	47400	2018-07-30 00:00:00	Jalan Masjid	Pelangi Utama	Condominium	2200	https://www.iproperty.com.my/property/bandar-u...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
3	1057	Petaling Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12252	101.628	2	3	...	47300	2018-07-30 00:00:00	Jalan SS 2/72	Ken Damansara 3	Condominium	1980	https://www.iproperty.com.my/property/petaling...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
4	1057	Petaling Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12252	101.628	2	3	...	47300	2018-07-30 00:00:00	Jalan SS 2/72	Ken Damansara 3	Condominium	1980	https://www.iproperty.com.my/property/petaling...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
5	0	Bandar Sunway	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.05930	101.616	2	2	...	47100	2018-07-30 00:00:00	Jalan Taylors	D' Latour	Condominium	2600	https://www.iproperty.com.my/property/bandar-s...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
5 rows × 21 columns

# check the summary info of the dataframe
rental.info()
<class 'pandas.core.frame.DataFrame'>
Int64Index: 54405 entries, 1 to 100736
Data columns (total 21 columns):
BuiltUpSize        54405 non-null int64
City               54391 non-null object
CreatedDateTime    54405 non-null object
CreatedUser        54405 non-null object
DataSourceID       54405 non-null int64
Furnishing         54405 non-null object
Latitude           54405 non-null float64
Longitude          54405 non-null float64
NoOfBathroom       54405 non-null int64
NoOfBedroom        54405 non-null int64
NoOfParking        54405 non-null int64
Postcode           54405 non-null object
PostedDate         54405 non-null object
PropertyAddress    54354 non-null object
PropertyName       54405 non-null object
PropertyType       54405 non-null object
RentalPerMth       54405 non-null int64
SourceUrl          54405 non-null object
State              54405 non-null object
UpdatedDateTime    54405 non-null object
UpdatedUser        54405 non-null object
dtypes: float64(2), int64(6), object(13)
memory usage: 9.1+ MB
Drop Missing Values
# check for missing values
rental.isnull().sum()
BuiltUpSize         0
City               14
CreatedDateTime     0
CreatedUser         0
DataSourceID        0
Furnishing          0
Latitude            0
Longitude           0
NoOfBathroom        0
NoOfBedroom         0
NoOfParking         0
Postcode            0
PostedDate          0
PropertyAddress    51
PropertyName        0
PropertyType        0
RentalPerMth        0
SourceUrl           0
State               0
UpdatedDateTime     0
UpdatedUser         0
dtype: int64
# drop missing values
rental = rental.dropna()
rental = rental.reset_index(drop=True)
# check for missing values again
rental.isnull().sum()
BuiltUpSize        0
City               0
CreatedDateTime    0
CreatedUser        0
DataSourceID       0
Furnishing         0
Latitude           0
Longitude          0
NoOfBathroom       0
NoOfBedroom        0
NoOfParking        0
Postcode           0
PostedDate         0
PropertyAddress    0
PropertyName       0
PropertyType       0
RentalPerMth       0
SourceUrl          0
State              0
UpdatedDateTime    0
UpdatedUser        0
dtype: int64
Remove Duplicate Values
print("There are {} duplicate values.".format(rental.duplicated().sum()))
rental[rental.duplicated(keep=False)].head(10)
There are 25229 duplicate values.
BuiltUpSize	City	CreatedDateTime	CreatedUser	DataSourceID	Furnishing	Latitude	Longitude	NoOfBathroom	NoOfBedroom	...	Postcode	PostedDate	PropertyAddress	PropertyName	PropertyType	RentalPerMth	SourceUrl	State	UpdatedDateTime	UpdatedUser
0	1000	Bandar Utama	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.14195	101.616	2	3	...	47400	2018-07-30 00:00:00	Jalan Masjid	Pelangi Utama	Condominium	2200	https://www.iproperty.com.my/property/bandar-u...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
1	1000	Bandar Utama	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.14195	101.616	2	3	...	47400	2018-07-30 00:00:00	Jalan Masjid	Pelangi Utama	Condominium	2200	https://www.iproperty.com.my/property/bandar-u...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
2	1057	Petaling Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12252	101.628	2	3	...	47300	2018-07-30 00:00:00	Jalan SS 2/72	Ken Damansara 3	Condominium	1980	https://www.iproperty.com.my/property/petaling...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
3	1057	Petaling Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12252	101.628	2	3	...	47300	2018-07-30 00:00:00	Jalan SS 2/72	Ken Damansara 3	Condominium	1980	https://www.iproperty.com.my/property/petaling...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
4	0	Bandar Sunway	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.05930	101.616	2	2	...	47100	2018-07-30 00:00:00	Jalan Taylors	D' Latour	Condominium	2600	https://www.iproperty.com.my/property/bandar-s...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
5	0	Bandar Sunway	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.05930	101.616	2	2	...	47100	2018-07-30 00:00:00	Jalan Taylors	D' Latour	Condominium	2600	https://www.iproperty.com.my/property/bandar-s...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
7	1248	Subang Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Partly Furnished	3.05238	101.599	3	0	...	47500	2018-07-29 00:00:00	Jalan Subang Permai	The Regina	Condominium	1700	https://www.iproperty.com.my/property/subang-j...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
8	1248	Subang Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Partly Furnished	3.05238	101.599	3	0	...	47500	2018-07-29 00:00:00	Jalan Subang Permai	The Regina	Condominium	1700	https://www.iproperty.com.my/property/subang-j...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
10	1488	Ara Damansara	2018-08-04 09:31:52	SYSTEM_TH	4	Partly Furnished	3.11319	101.579	3	3	...	47301	2018-07-29 00:00:00	Jalan Pju 1A/4	The Potpourri	Condominium	4500	https://www.iproperty.com.my/property/ara-dama...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
11	1488	Ara Damansara	2018-08-04 09:31:52	SYSTEM_TH	4	Partly Furnished	3.11319	101.579	3	3	...	47301	2018-07-29 00:00:00	Jalan Pju 1A/4	The Potpourri	Condominium	4500	https://www.iproperty.com.my/property/ara-dama...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
10 rows × 21 columns

# remove duplicate values
rental = rental.drop_duplicates()
rental = rental.reset_index(drop=True)
# check for duplicate values after removing duplicates
print("There are {} duplicate values.".format(rental.duplicated().sum()))
rental.head()
There are 0 duplicate values.
BuiltUpSize	City	CreatedDateTime	CreatedUser	DataSourceID	Furnishing	Latitude	Longitude	NoOfBathroom	NoOfBedroom	...	Postcode	PostedDate	PropertyAddress	PropertyName	PropertyType	RentalPerMth	SourceUrl	State	UpdatedDateTime	UpdatedUser
0	1000	Bandar Utama	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.14195	101.616	2	3	...	47400	2018-07-30 00:00:00	Jalan Masjid	Pelangi Utama	Condominium	2200	https://www.iproperty.com.my/property/bandar-u...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
1	1057	Petaling Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12252	101.628	2	3	...	47300	2018-07-30 00:00:00	Jalan SS 2/72	Ken Damansara 3	Condominium	1980	https://www.iproperty.com.my/property/petaling...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
2	0	Bandar Sunway	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.05930	101.616	2	2	...	47100	2018-07-30 00:00:00	Jalan Taylors	D' Latour	Condominium	2600	https://www.iproperty.com.my/property/bandar-s...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
3	0	Ara Damansara	2018-08-04 09:31:52	SYSTEM_TH	4	Fully Furnished	3.12444	101.583	1	1	...	47301	2018-07-29 00:00:00	Jalan PJU 1A	Eve Suite / NZX Square	Serviced Residence	1800	https://www.iproperty.com.my/property/ara-dama...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
4	1248	Subang Jaya	2018-08-04 09:31:52	SYSTEM_TH	4	Partly Furnished	3.05238	101.599	3	0	...	47500	2018-07-29 00:00:00	Jalan Subang Permai	The Regina	Condominium	1700	https://www.iproperty.com.my/property/subang-j...	Selangor	2018-08-04 09:31:52	SYSTEM_TH\r
5 rows × 21 columns

Clean Data
We will use seven features i.e. "BuiltUpSize", "NoOfBathroom", "NoOfBedroom", "NoOfParking", "State", "Furnishing", "PropertyType" to predict rental prices i.e. "RentalPerMth". Therefore, we will need to do some data cleaning to correct any spelling errors or typos in these 8 columns.

# select columns that we want to work with
rental = rental[["BuiltUpSize", "NoOfBathroom", "NoOfBedroom", "NoOfParking", "State", "Furnishing", "PropertyType", "RentalPerMth"]]
rental.head(10)
BuiltUpSize	NoOfBathroom	NoOfBedroom	NoOfParking	State	Furnishing	PropertyType	RentalPerMth
0	1000	2	3	1	Selangor	Fully Furnished	Condominium	2200
1	1057	2	3	1	Selangor	Fully Furnished	Condominium	1980
2	0	2	2	0	Selangor	Fully Furnished	Condominium	2600
3	0	1	1	2	Selangor	Fully Furnished	Serviced Residence	1800
4	1248	3	0	2	Selangor	Partly Furnished	Condominium	1700
5	0	2	2	2	Selangor	Fully Furnished	Serviced Residence	2500
6	1488	3	3	3	Selangor	Partly Furnished	Condominium	4500
7	871	2	2	2	Selangor	Fully Furnished	Condominium	2500
8	0	2	3	2	Selangor	Partly Furnished	Condominium	1500
9	544	1	1	0	Selangor	Partly Furnished	Condominium	1200
# check for typo or wrong spelling for State column
rental["State"].value_counts()
selangor        16206
kuala-lumpur     7237
johor            5514
putrajaya         134
Selangor           20
Name: State, dtype: int64
# replace typo with the correct spelling
rental["State"] = rental["State"].replace("Selangor", "selangor")
# recheck again
rental["State"].value_counts()
selangor        16226
kuala-lumpur     7237
johor            5514
putrajaya         134
Name: State, dtype: int64
# check for typo or wrong spelling for Furnishing column
rental["Furnishing"].value_counts()
Fully Furnished     15553
Partly Furnished     9319
Unfurnished          4192
Unknown                47
Name: Furnishing, dtype: int64
# drop Unknown Furnishing values
rental = rental[rental["Furnishing"] != "Unknown"].reset_index(drop=True)
# recheck again
rental["Furnishing"].value_counts()
Fully Furnished     15553
Partly Furnished     9319
Unfurnished          4192
Name: Furnishing, dtype: int64
# check for typo or wrong spelling for Furnishing column
rental["PropertyType"].value_counts()
Serviced Residence            11901
Condominium                   11615
Apartment                      2576
2-sty Terrace/Link House       1185
Semi-detached House             483
Bungalow                        342
3-sty Terrace/Link House        237
Townhouse                       158
Flat                            145
Cluster House                   139
1-sty Terrace/Link House        116
2.5-sty Terrace/Link House      109
1.5-sty Terrace/Link House       25
4-sty Terrace/Link House         18
3.5-sty Terrace/Link House       11
Residential Land                  4
Name: PropertyType, dtype: int64
# Since we are working with property data, it is impossible or does not make sense to have 0 values for certain columns
# we need to drop observations with value = 0 for these columns

# drop BuiltUpSize = 0
rental = rental[rental["BuiltUpSize"] != 0].reset_index(drop=True)

# drop NoOfBedroom = 0
rental = rental[rental["NoOfBedroom"] != 0].reset_index(drop=True)

# drop NoOfBathroom = 0
rental = rental[rental["NoOfBathroom"] != 0].reset_index(drop=True)

# drop NoOfParking = 0
rental = rental[rental["NoOfParking"] != 0].reset_index(drop=True)

# drop RentalPerMth = 0
rental = rental[rental["RentalPerMth"] != 0].reset_index(drop=True)

rental.head(10)
BuiltUpSize	NoOfBathroom	NoOfBedroom	NoOfParking	State	Furnishing	PropertyType	RentalPerMth
0	1000	2	3	1	selangor	Fully Furnished	Condominium	2200
1	1057	2	3	1	selangor	Fully Furnished	Condominium	1980
2	1488	3	3	3	selangor	Partly Furnished	Condominium	4500
3	871	2	2	2	selangor	Fully Furnished	Condominium	2500
4	700	1	1	1	selangor	Partly Furnished	Condominium	1300
5	1021	2	3	1	selangor	Fully Furnished	Serviced Residence	2500
6	1100	2	3	2	selangor	Partly Furnished	Condominium	2500
7	850	1	1	1	kuala-lumpur	Fully Furnished	Serviced Residence	2100
8	791	2	2	2	kuala-lumpur	Fully Furnished	Serviced Residence	1900
9	888	2	2	1	kuala-lumpur	Fully Furnished	Serviced Residence	3000
# check the summary info of the dataframe after cleaning the data
rental.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 13086 entries, 0 to 13085
Data columns (total 8 columns):
BuiltUpSize     13086 non-null int64
NoOfBathroom    13086 non-null int64
NoOfBedroom     13086 non-null int64
NoOfParking     13086 non-null int64
State           13086 non-null object
Furnishing      13086 non-null object
PropertyType    13086 non-null object
RentalPerMth    13086 non-null int64
dtypes: int64(5), object(3)
memory usage: 818.0+ KB
Check For Outliers
# take a look at the statistics of the RentalPerMth column
with pd.option_context('float_format', '{:f}'.format): print(rental["RentalPerMth"].describe())
count     13086.000000
mean       2879.990601
std       21623.624529
min         110.000000
25%        1400.000000
50%        1800.000000
75%        2500.000000
max     1240000.000000
Name: RentalPerMth, dtype: float64
Note: The values range from 110 up to 1,240,000 with a median value of only 1,800 indicates that there might be presence of outliers in the dataset.

# create distribution plot and boxplot to check for outliers

plt.subplot(121)
sns.distplot(rental["RentalPerMth"]);

plt.subplot(122)
rental["RentalPerMth"].plot.box(figsize=(16,5))

plt.show()

Note: Indeed there are outliers in the RentalPerMth column and they will distort the models to be built later. To overcome this, we will only take into account RentalPerMth less than RM10,000 to be used in model building.

# limit the rentalPerMonth to be less than or equal to RM10,000
rental = rental[rental["RentalPerMth"] <= 10000].reset_index(drop=True)

print("Data type is {0} \n\nShape of dataframe is {1}\n".format(type(rental), rental.shape))
Data type is <class 'pandas.core.frame.DataFrame'> 

Shape of dataframe is (13016, 8)

Note: The data points have been reduced from 13,086 to 13,016 i.e. 70 outliers have been removed, which is only 0.5% of original data removed.

# have a look at the statistics of the RentalPerMth column
with pd.option_context('float_format', '{:f}'.format): print(rental["RentalPerMth"].describe())
count   13016.000000
mean     2090.654348
std      1072.684678
min       110.000000
25%      1400.000000
50%      1800.000000
75%      2500.000000
max     10000.000000
Name: RentalPerMth, dtype: float64
Note: The mean and standard deviation have dropped significantly to 2,090 and 1,072 from 2,879 and 21,623 previously. Let's visualize the distribution again.

# create distribution plot and boxplot to check for outliers

plt.subplot(121)
sns.distplot(rental["RentalPerMth"]);

plt.subplot(122)
rental["RentalPerMth"].plot.box(figsize=(16,5))

plt.savefig("distribution.png", bbox_inches="tight")

plt.show()

# visualize the relationship using scatter plots

plt.figure(figsize=(14, 8))

plt.subplot(2, 2, 1)
plt.scatter(x=rental["BuiltUpSize"], y=rental["RentalPerMth"])
plt.xlabel("BuiltUpSize")
plt.ylabel("RentalPerMth")

plt.subplot(2, 2, 2)
plt.scatter(x=rental["NoOfBathroom"], y=rental["RentalPerMth"])
plt.xlabel("NoOfBathroom")
plt.ylabel("RentalPerMth")

plt.subplot(2, 2, 3)
plt.scatter(x=rental["NoOfBedroom"], y=rental["RentalPerMth"])
plt.xlabel("NoOfBedroom")
plt.ylabel("RentalPerMth")

plt.subplot(2, 2, 4)
plt.scatter(x=rental["NoOfParking"], y=rental["RentalPerMth"])
plt.xlabel("NoOfParking")
plt.ylabel("RentalPerMth")

plt.savefig("scatterplots.png", bbox_inches="tight")
plt.show()

# calculate correlation matrix
corr = rental[["BuiltUpSize", "NoOfBathroom", "NoOfBedroom", "NoOfParking", "RentalPerMth"]].corr()
corr
BuiltUpSize	NoOfBathroom	NoOfBedroom	NoOfParking	RentalPerMth
BuiltUpSize	1.000000	0.712336	0.647470	0.405194	0.399175
NoOfBathroom	0.712336	1.000000	0.723849	0.376014	0.258629
NoOfBedroom	0.647470	0.723849	1.000000	0.347061	0.052842
NoOfParking	0.405194	0.376014	0.347061	1.000000	0.112475
RentalPerMth	0.399175	0.258629	0.052842	0.112475	1.000000
# visualize correlation matrix
plt.subplots(figsize=(8,6))

fig = sns.heatmap(corr, 
            xticklabels=corr.columns.values,
            yticklabels=corr.columns.values)

fig.get_figure().savefig("corr_heatmap.png", bbox_inches="tight") 

Note: Built Up Size has the highest correlation (positive) with Rental Per Month as expected. Surprisingly, No of Bathroom has higher correlation with Rental Per Month compared to No of Bedroom.

One Hot Encoding
Before we build the model, we need to create dummy variables for the three categorical variables: "State", "Furnishing", "PropertyType". We need to perform one hot encoding for the categorical variables by using pandas get_dummies() function.

# one hot encoding
rental_df = pd.get_dummies(rental)

rental_df.head()
BuiltUpSize	NoOfBathroom	NoOfBedroom	NoOfParking	RentalPerMth	State_johor	State_kuala-lumpur	State_putrajaya	State_selangor	Furnishing_Fully Furnished	...	PropertyType_4-sty Terrace/Link House	PropertyType_Apartment	PropertyType_Bungalow	PropertyType_Cluster House	PropertyType_Condominium	PropertyType_Flat	PropertyType_Residential Land	PropertyType_Semi-detached House	PropertyType_Serviced Residence	PropertyType_Townhouse
0	1000	2	3	1	2200	0	0	0	1	1	...	0	0	0	0	1	0	0	0	0	0
1	1057	2	3	1	1980	0	0	0	1	1	...	0	0	0	0	1	0	0	0	0	0
2	1488	3	3	3	4500	0	0	0	1	0	...	0	0	0	0	1	0	0	0	0	0
3	871	2	2	2	2500	0	0	0	1	1	...	0	0	0	0	1	0	0	0	0	0
4	700	1	1	1	1300	0	0	0	1	0	...	0	0	0	0	1	0	0	0	0	0
5 rows × 27 columns

# check summary info to see if one hot encoding is done properly
print(rental_df.shape, "\n")
rental_df.info()
(13016, 27) 

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 13016 entries, 0 to 13015
Data columns (total 27 columns):
BuiltUpSize                                13016 non-null int64
NoOfBathroom                               13016 non-null int64
NoOfBedroom                                13016 non-null int64
NoOfParking                                13016 non-null int64
RentalPerMth                               13016 non-null int64
State_johor                                13016 non-null uint8
State_kuala-lumpur                         13016 non-null uint8
State_putrajaya                            13016 non-null uint8
State_selangor                             13016 non-null uint8
Furnishing_Fully Furnished                 13016 non-null uint8
Furnishing_Partly Furnished                13016 non-null uint8
Furnishing_Unfurnished                     13016 non-null uint8
PropertyType_1-sty Terrace/Link House      13016 non-null uint8
PropertyType_1.5-sty Terrace/Link House    13016 non-null uint8
PropertyType_2-sty Terrace/Link House      13016 non-null uint8
PropertyType_2.5-sty Terrace/Link House    13016 non-null uint8
PropertyType_3-sty Terrace/Link House      13016 non-null uint8
PropertyType_4-sty Terrace/Link House      13016 non-null uint8
PropertyType_Apartment                     13016 non-null uint8
PropertyType_Bungalow                      13016 non-null uint8
PropertyType_Cluster House                 13016 non-null uint8
PropertyType_Condominium                   13016 non-null uint8
PropertyType_Flat                          13016 non-null uint8
PropertyType_Residential Land              13016 non-null uint8
PropertyType_Semi-detached House           13016 non-null uint8
PropertyType_Serviced Residence            13016 non-null uint8
PropertyType_Townhouse                     13016 non-null uint8
dtypes: int64(5), uint8(22)
memory usage: 788.2 KB
Train and Build Models
We will build several models to predict "RentalPerMonth" as target variable using seven features i.e. "BuiltUpSize", "NoOfBathroom", "NoOfBedroom", "NoOfParking", "State", "Furnishing", and "PropertyType".

# separate data into X features and Y target
X = rental_df.drop(columns=["RentalPerMth"])
Y = rental_df["RentalPerMth"]
# split data into random train and test subsets

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size=0.25, random_state=0)
Linear Regression
# fit a Linear Regression Model
from sklearn.linear_model import LinearRegression
regressor = LinearRegression()
regressor.fit(X_train, y_train)
LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)
# make predictions
y_pred = regressor.predict(X_test)
# calculate R-squared
print("Liner Regression R-squared: {}".format(regressor.score(X_test, y_test)))
Liner Regression R-squared: 0.4496332622653895
# another method of calculating R-squared
from sklearn.metrics import r2_score
lin_r2 = r2_score(y_test, y_pred)

print("Liner Regression R-squared: {}".format(lin_r2))
Liner Regression R-squared: 0.4496332622653896
Note: The R-squared shows the goodness of fit of the model, it indicates that 44.96% of the variability in Y (Rental Per Month) can be explained using X (the seven features i.e. "BuiltUpSize", "NoOfBathroom", "NoOfBedroom", "NoOfParking", "State", "Furnishing", and "PropertyType".).

# calculate root mean squared error (RMSE)
from sklearn.metrics import mean_squared_error

lin_mse = mean_squared_error(y_pred, y_test)
lin_rmse = np.sqrt(lin_mse)
print("Liner Regression RMSE: {}".format(lin_rmse))
Liner Regression RMSE: 799.2724851986891
# calculate mean absolute error (MAE)
from sklearn.metrics import mean_absolute_error

lin_mae = mean_absolute_error(y_pred, y_test)
print("Liner Regression MAE: {}".format(lin_mae))
Liner Regression MAE: 521.5524841211809
# get feature coefficients
importance = regressor.coef_
feature_indexes_by_importance = importance.argsort()
for index in feature_indexes_by_importance:
    print('{} : {}'.format(X_train.columns[index], (importance[index] )))
PropertyType_3-sty Terrace/Link House : -1102.4157279163671
PropertyType_Bungalow : -1037.4929212507136
PropertyType_Cluster House : -853.32537163674
PropertyType_Semi-detached House : -397.7946647750431
State_johor : -372.0595869861387
NoOfBedroom : -335.7293411135535
PropertyType_2-sty Terrace/Link House : -288.92200805683234
Furnishing_Partly Furnished : -274.7742123240336
PropertyType_1.5-sty Terrace/Link House : -179.00399967572247
State_selangor : -146.89350601762123
Furnishing_Unfurnished : -107.69077948809557
State_putrajaya : -3.556736066567613
NoOfParking : -1.817187537568867
BuiltUpSize : 1.4239215787244168
PropertyType_2.5-sty Terrace/Link House : 22.154976533329155
PropertyType_Townhouse : 106.1905732785825
PropertyType_Residential Land : 119.98328318066446
PropertyType_1-sty Terrace/Link House : 151.38673124692647
NoOfBathroom : 297.29528936095545
Furnishing_Fully Furnished : 382.4649918121911
PropertyType_Flat : 480.3798778084975
PropertyType_Apartment : 494.3207361482097
State_kuala-lumpur : 522.5098290700763
PropertyType_Condominium : 705.748802203372
PropertyType_4-sty Terrace/Link House : 852.5876385454096
PropertyType_Serviced Residence : 926.2020743677823
