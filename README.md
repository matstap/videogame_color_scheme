# Video Game Color Palettes

- [Problem Statement](#Problem-Statement)
- [Project Summary](#Project-Summary)
- [Project Files](#Project-Files)
- [Data Dictionary](#Data-Dictionary)
- [Data Collection](#Data-Collection)
- [Exploratory Data Analysis](#Exploratory-Data-Analysis)
- [Conclusions](#Conclusions)

---

### Problem Statement

I was inspired by this [study](https://online.ucpress.edu/jpms/article/30/4/161/106385/Quantitative-Sentiment-Analysis-of-Lyrics-in), which found that popular music is getting sadder, to explore if there are any trends in video game colors. The ultimate goal with this project is to incorporate color theory to track trends in color palette moods for the most popular video games. 

---

### Project Summary

I focused analysis on the top 100 popular video games for the years 2000 - 2019. Screenshots from each game were combined into single images and color palettes were generated using KMeans clustering to find the 5 most dominant colors. From here, I conducted analysis on perceived brightness and the color families represented in the images compared to rank, genre, platform and year. 

---

### Project Files

The project files are at this time hosted on this Drive [folder](https://drive.google.com/drive/folders/1Jh5yA75WYkdn9NWF5uLTf6OD235Py1Jr?usp=sharing):  

**Main Directory/**  
 - [DataCollection.ipynb](https://colab.research.google.com/drive/1MAivMyteNLvvJLDLyXlBPaPJpmcjMXmg?usp=sharing): Notebook code for scraping video game data using RAWG.io API
 - [Image_Compilation.ipynb](https://colab.research.google.com/drive/14t0RqQ4uLylOrFbY6fxKPHWmX2lW35mZ?usp=sharing): Notebook code for concatenating screenshots into one image per game
 - [Color_Palette.ipynb](https://colab.research.google.com/drive/101M6x0xYZ-7Nh4KTofomi1PtbofcLbMj?usp=sharing): Notebook code for extracting color palettes from concatenated screenshots
 - [EDA.ipynb](https://colab.research.google.com/drive/13zVKsR05YKNLvKtipfBk1IpgE5P_dVAP?usp=sharing): Notebook code for main EDA and analysis  
 - [PaletteVisuals.py](https://colab.research.google.com/drive/1bQ85lMnzGT32M8DYHOhYMdFLbT8CIsJq?usp=sharing): Python script containing project specific functions to reduce excess code in project notebooks and scripts
 - [functions.py](https://drive.google.com/file/d/1FYeraJo29iAGxZefTgiZvWucrj7chy5x/view?usp=sharing): Python script containing project specific functions to reduce excess code in project notebooks
- **Data**/
 - [games-raw.csv](https://drive.google.com/file/d/1e25Cc8GHa6UfCeu0RNgRqdV05MKHLO9N/view?usp=sharing): Raw data collected from RAWG.io database
 - [games_focused.csv](https://drive.google.com/file/d/1zz8CfBzgFOScNWCOJcotVKP9YtIsy3KF/view?usp=sharing): Only the fields of use to my analysis from games-raw.csv plus links to concatenated screenshots
 - [complete.csv](https://drive.google.com/file/d/1TJO6O-PirfblevjqN2FQknqC4XurCOLA/view?usp=sharing): Adds "colors" and "counts" fields, which are the colors and weights in each palette 
 - [final.csv](https://drive.google.com/file/d/1uGPaXASGIUgFtgCskKZo7J8BW6KjZ8zG/view?usp=sharing): Adds color family fields. Explained in data dictionary
- **Images**/
 - Directory containing image files of concatenated screenshots

---

### Data Dictionary

Data dictionary for final.csv:

|Feature|Type|Description|
|---|---|---|
name|str|Video game name
platform|str|Platforms game available in
released|datetime|Release date
year|int|Year of released date
rating|float|Critic rating
id|int|Identifying key
genres|str|Game's genres
main_genre|str|Main listed genres
combined_image|str|Filename of concatenated screenshots
colors|list|Colors in palette in order of dominance
counts|list|Weights of colors in palette
brightness|float|Perceived brightness of color palette (0.0 - 255.0)
r|float|Poportion of palette falling under the red category
org|float|Poportion of palette falling under the orange category
y|float|Poportion of palette falling under the yellow category
g|float|Poportion of palette falling under the green category
bl|float|Poportion of palette falling under the blue category
m|float|Poportion of palette falling under the magenta category
purp|float|Poportion of palette falling under the purple category
brn|float|Poportion of palette falling under the brown category
grey|float|Poportion of palette falling under the grey category


---

### Data Collection

All data was collected from RAWG.io using their [API](https://api.rawg.io/docs/). The API endpoints for all game data for a certain year in order of popularity rank is "https:\//api.rawg.io/api/games?dates={year}-01-01,{year}-12-31". To get the top 100, for each year I made 5 requests with the parameters {'page': page, 'page_size': 20}, as the max results per request is 40.

#### Image Compilation 

The screenshots were given as URL's. For each game in the dataset, I received each screenshot using urllib.request.urlopen, converted them to numpy arrays, then used numpy's hstack to combine all image arrays into one image array. From here, the image is saved to the Image folder and the filename is added to the dataframe for that game.

#### Color Palette Generation

I used KMeans clustering to identify the top 5 colors in each image. Each is resized to 100X100 pixels to speed up fitting the KMeans model, in which the labels are the colors. The 5 labels (colors) with the highest frequencies are stored in a list, as well as the subsequent frequencies for each label. The colors are converted in digit format to avoid issues I was having wen writing the data to a csv.

---

### Exploratory Data Analysis

#### Brightness 

The perceived brightness of a color can be found using this formula: $\sqrt{0.299R**2 + 0.587G**2 + 0.114B**2}$

This is a weighted Euclidean distance from black to white of a given color. This was calculated for each color in a game's palette and the average brightness, weighted by each colors frequency, is saved to the data frame.

When analyzing brightness by year, platform, genre and rank, the distributions for all were normal centered around similar means. So there is no evidence I have found so far that there are any trends in perceived brightness. The one interesting observation is that the average perceived brightness for each platform was around 80, while Nintendo's was more than 100. I assume this is because of Nintendo's focus less on realism and graphical power and more on incorporating brighter colors and innovative animation $styles$ to use their consoles' technical limitations to their advantage.

#### Color Family 

I decided to explore the proportion of colors in a color family in each palette. The “family” a color belongs to is one of a set of colors which has the smallest RGB Euclidean distance from the given color. I determined that the colors Red, Orange, Yellow, Green, Blue, Magenta, Purple, Brown and Grey would be sufficient families based on their locations on the color wheel.

As seen with the brightness, Nintendo stands out here. The average color family proportions for the color palettes in exclusives for the other platforms were overwhelmingly in the brown and grey families. This was mostly the case for Nintendo as well, but it also had a good proportion of yellow and purple.

The large average proportion of brown and grey was also seen for each genre. When looking at the color family proportions without brown and grey, casual and educational games had the most use of color. Another interesting genre was simulation as it was the only one with noticeable use of the green family. 

---

### Conclusions

While some interesting observations were made, I need more data to identify any meaningful trends. Ideally, I want to collect at least 100 screenshots from each game spanning every section of the game. This way, I can make color palettes for each section and make analysis more granular.

I also plan on improving the clustering method to extract color palettes. I do not think that the most frequent color is necessarily the best choice for most dominant. Imagine an image or a red ball taking up 1/5 of the area with an all white background. I would naturally think the dominant color here is red, but my current model would say it is white. I will also explore more ways to determine color family.

Once I have sufficient data and better clustering, I plan on applying color mood theory to analyze possible trends in video game color mood, especially against trends in popular music sentiment. 
