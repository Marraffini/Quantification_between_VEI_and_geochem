# Quantification_between_VEI_and_geochem
where the data come from (+ dates) + static versions of the data (cant use more newer versions of the data)
what is the goal of the project
author of code
aknowledgments

## Data (from where and which date)

-> AVO database comes from this website (I can't seem to find the link from where I downloaded it).

-> GVP data has two files, one xlsx and one csv. 
    GVP.xlsx was downloaded on 11 April 2025, from this link https://volcano.si.edu/database/GVP_Eruption_Search_Result.xlsx or this link https://volcano.si.edu/search_eruption.cfm.
    GVP_Volcano_List.csv was downloaded on 13 May 2025, from this link https://volcano.si.edu/search_volcano.cfm. 
    
-> GEOROC data has lots of files, depending on the type and sub type of each rock.
It was downloaded from this website, following : Query By > Petrography and samples > Volcanic rocks and downloading all the different type of rocks. https://georoc.eu/georoc/new-start.asp


## What script do we need and what does each script do ?

### GEOROC

There are many rmd for this particular database. For each type of rocks, actually. I will explain with one type, since the process is the same for the rest. 
Say we want to work on the alkaline type of rock. Rmd is called alkaline rocks.rmd. 
In the alkaline rocks, there are different types of sub rocks. I don't know why, but when I downloaded the csv, it was either the csv type or csv2 type. Put them in different folders (for the code to work).
I then created two different csv/df within those subrock csv, one for the samples and one for the references. 
This should be something like this : ALKALINE --> analcimit --> ref.csv
                                                      |
                                                      Y
                                                      samples.csv
                                                      
                                              --> leucitit --> ref.csv
                                                    |
                                                    samples.csv
                                                    
                                                    
The csv are not called samples and ref but this is just to show how it is.

I bind all the references together, aswell as the samples. 

In the samples.csv I keep all ten major oxides. 
I filtered all the rows where the oxides are not completed.
I also filtered when the geological age is too much. 
I added back the references, so the samples csv is now classified by references.

For the references csv, I used the samples csv to filter out the unnecessary references. 


WARNING : I do think that this should work even the a newer version of the data is used. 

### AVO

We will need two different rmd, avo.rmd and avo_filtered.rmd.

-> In avo.rmd, I used the avo.csv that was downloaded from the website, this is the raw data. 

avo_with_confirmed_eruption_and_vei_250625 is created in the rmd avo and constructed as such :

- only keep useful columns
- delete rows where the 10 majors oxides are all NA
- clean some rows (sometimes in oxides, there was text before the data nb so i got rid of the text)
- i filtered and drop the volcanoes that weren't volcanoes
- i only kept the existing eruption (so if it was NA, i dropped)
 
At this point, I have 80 unique eruptions for 30 unique volcanoes and 1226 samples.
 
 
Then I completed the oxides if some oxides value was missing : 
 
(Clean oxide and Total-majors columns by extracting numeric values.)

For each row:

- If exactly 1 oxide value is missing → impute it so that the sum equals 100%. and if its negative value then put 0
- If exactly 2 oxide values are missing → normalize existing values to sum to 100%.
- If more than 2 oxide values are missing → discard the row.

Finally, update Total-majors only if it's missing, using the sum of oxide values.


I dropped all the rows where the sum_oxide is less than 95% and more than 100.5%.

We use sum_oxide and not total-majors.


eruption_summary is created by counting the nb of samples for each eruption. 

View of eruption summary : 

Eruption            nb of samples         Volcano Name

Novarupta 1912/6	  290	                  Katmai, Novarupta		
Aniakchak 1931/5	  107	                  Aniakchak		
Redoubt 1989/12	    90	                  Redoubt		
Augustine 2005/12	  89	                  Augustine		
Trident 1953/2	    88                  	Trident


Then I searched the VEI of every eruption (so far, we have 80 unique eruptions) and added it in eruption_summary.

I checked with Mark, then I deleted some eruptions where I couldn't find the VEI. 
--> So I dropped 17 eruptions, and it was linked to 41 rows (to drop), so we went from 1226 samples to 1185 samples.
--> We have now 63 eruptions from 29 volcanoes. 


I filtered out eruption_summary from avo_with_confirmed_eruption to create avo_with_confirmed_eruption_and_vei_250625.
--> i used the eruption column and join/left join


Therefore, avo_with_confirmed_eruption_and_vei_250625 was created. 

--> In avo_filtered, I used avo_with_confirmed_eruption_and_vei_250625.
For the eruption where I only have one sample (15 eruptions), i tried to search more samples, but only could do so for 3. 


So these rmd are used to create the "cleaned data" we will work on. 


We will use 3 more rmd, called as such : avo mean.rmd, avo sd.rmd, avo mean sd.rmd.
In those rmds, we have made the alr transformation, scaled the data, took the mean, the cube root sd and both, and then created splits and folds. Before, we would have added the eruptions of vei 7.
To what use ? --> to use those data for prediction models.


### GVP

We can't really use GVP alone, it needs to be paired with the info of GEOROC if we want to do something with it. 
What is the most logical thing to do is to only keep the "Confirmed Eruption" and then create different rmd according to the VEI we want to work with. Then, search in the LOCATION/LOCATION COMMENT of GEOROC the name of the volcano from GVP to somewhat link the two.

We did that for the VEI 7, and we could do that for the other VEI, but there is a lot of eruptions of VEI 6 already and we need to check which samples of GEOROC are really linked to the eruption provided by GVP. 
What I mean is that since the only way to efficiently "link" GVP and GEOROC is to search for the Volcano Name of GVP in the LOCATION/LOCATION COMMENT, errors can be made and it will not distinct/classify the samples according to the VEI of the eruptions. It can't do that, it will only link the samples to a volcano and not an eruption and we want the samples to be linked to an eruption first.
So, to do that, we need to look manually at the references of each sample and check what is the origin of each sample. 
Since there hasn't been a lot of VEI 7 eruption, it was quite alright to do, but even from VEI 6, this is very hard, since there are a lot more references to check.

So you need to use the data for the vei 7 provided here, or you can do it by hand again if you use a newer version of the GVP and GEOROC. 

I will leave the rmd for inspiration. 

The data you should use is rocks_all_info_vei_7.csv.







