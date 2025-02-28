# TikTok hashtag analysis toolset 
The tool helps to download posts and videos from TikTok for a given set of hashtags over a period of time. Users can create a growing database of posts for specific hashtags which can then be used for further hashtag analysis. It uses the [tiktok-scraper](https://github.com/drawrowfly/tiktok-scraper) Node package  to download the posts and videos.

## Pre-requisites
1. Make sure you have Python 3.6 or a later version installed
2. Download and install TikTok scraper: https://github.com/drawrowfly/tiktok-scraper 
3. (Optional) create and activate a virtual environment for this tool, for example by executing the following command, which creates the `.env` virtual environment in the tool's root directory:

    `python3 -m venv .env`

4. Start your virtual environment
    - On Unix-like operating systems (macOS, Linux), this can be done using the command `source .env/bin/activate`
    - On Windows, this can be done using the command `.env\Scripts\activate.bat`
    
5. Install the Python package dependencies for this tool by executing the command: 

    `pip install -r requirements.txt`

You should now be ready to start using the tool.


## About the tool
### Command-line arguments
```
python3 run_downloader.py --help
usage: run_downloader.py [-h] [-t [T [T ...]]] [-f F] [-p] [-v]

Download the tiktoks for the requested hashtags

optional arguments:
  -h, --help      show this help message and exit
  -t [T [T ...]]  List of hashtags to scrape
  -f F            File name containing list of hashtags to scrape
  -p              Download post data
  -v              Download video files
```

### Structure of output data
```
$ tree ../data
../data
├── ids
│   └── post_ids.json
├── london
│   └── posts
│       └── data.json
├── newyork
│   └── posts
│       └── data.json
└── paris
    └── posts
        └── data.json
```


The `data` folder contains all the downloaded data as shown in the tree diagram above. 
- The `ids` folder contains two files `post_ids.json` and `video_ids.json` that record the ids of the downloaded posts and videos for each hashtag.
- Each hashtag has a folder with two subfolders `posts` and `videos` that store posts and videos respectively. The posts are stored in the `data.json` file in the `posts` folder, and videos are stored as the `.mp4` files in the `videos` folder.


## How to use
### Post downloading
Running the `run_downloader.py` script with the following options will scrape posts containing the hashtags `#london`, `#paris`, or `#newyork`:

    python3 run_downloader.py -t london paris newyork -p

and will produce an output similar to the following log:

    $ python3 run_downloader.py -t london paris newyork -p
    Hashtags to scrape: ['london', 'paris', 'newyork']
    Scraped 963 posts containing the hashtag 'london'
    Scraped 961 posts containing the hashtag 'paris'
    Scraped 940 posts containing the hashtag 'newyork'
    Successfully scraped 2864 total entries

- The `-t` flag allows a space-separated list of hashtags to be specified as a command line argument
- The `-p` flag specifies that posts, not videos, will be downloaded

### Video downloading
Running the `run_downloader.py` script with the following options will scrape trending videos containing the hashtag `#london`:
` python3 run_downloader.py -t london -v`

- The `-t` flag allows a space-separated list of hashtags to be specified as a command line argument
- The `-v` flag specifies that videos, not posts, will be downloaded

Note that video downloading is a time and data rate consuming task, as a result we recommend using one hashtag at a time when using the `-v` flag to avoid complications.

## Analyzing results 
### Top n hashtag occurrences 
The script `hashtag_frequencies.py` analyzes the frequencies of top occurring hashtags in a given set of posts.

```
$ python3 hashtag_frequencies.py --help
usage: hashtag_frequencies.py [-h] [-p] [-d] hashtag n

positional arguments:
  hashtag      The hashtag of scraped posts to analyze
  n            The number of top n occurrences

optional arguments:
  -h, --help   show this help message and exit
  -p, --plot   Plot the occurrences
  -d, --print  List top n hashtags
  ```

Assume we want to analyze the 20 most frequently occurring hashtags in the downloaded posts of the `#london` hashtag.

- The results can be plotted and saved as a PNG file by executing the following command: 

    `python3 hashtag_frequencies.py london 20 -p`
    
    which will produce a figure similar to that shown below:
    <p align="center">
        <img src="https://user-images.githubusercontent.com/18430739/166878928-d146b352-b68c-4ab4-bd2c-feb2f0140df9.png" height="500" alt="Top 20 most frequent common hashtags in posts containing the #london hashtag">
    </p>
    
    In the above plot, the highest occurrence is the `#fyp` hashtag, which is tagged in more than half of all posts containing the `#london` hashtag.

- The results can be displayed in tabular form by executing the following command:

    `python3 hashtag_frequencies.py london 20 -d`

    which will produce a terminal output similar to the following:
    ```
    Rank     Hashtag                        Occurrences     Frequency
    0        london                         960             1.0000
    1        fyp                            494             0.5146
    2        uk                             238             0.2479
    3        foryou                         221             0.2302
    4        foryoupage                     184             0.1917
    5        viral                          179             0.1865
    6        fypシ                           84              0.0875
    7        funny                          56              0.0583
    8        xyzbca                         51              0.0531
    9        british                        45              0.0469
    10       england                        44              0.0458
    11       trending                       40              0.0417
    12       fy                             33              0.0344
    13       comedy                         32              0.0333
    14       roadman                        28              0.0292
    15       4u                             27              0.0281
    16       usa                            26              0.0271
    17       tiktok                         26              0.0271
    18       travel                         21              0.0219
    19       america                        20              0.0208
    Total posts: 960
    ```

    The `Frequency` column shows the ratio of the occurrence to the total number of downloaded posts.
