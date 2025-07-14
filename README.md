# lyric-based-music-genre-classifier

Please follow link to download files:
https://drive.google.com/drive/folders/1SUxz_Q5Teit1OVpJH8ZB6lnCwqF5kGu4?usp=drive_link}

Instructions for Team 157 Class Project:

There are 2 main folders containing the 2 main projects. The folders are
each self-contained (although they may have similar structures and files):

    1 - CODE\model-training
    2 - CODE\visualization

1) CODE\model-training

    DESCRIPTION:

    This package contains the code and supporting files needed to
    build, train, and demonstrate use of the models. This package is in jupyter
    notebook format. The main file to run is main.ipynb, and it includes comments
    and specific instructions for how to run within the notebook itself.

    The code in main.ipynb starts with a global variables section, which allows
    the user to set the required variables for training and testing the models,
    as well as instructing which parts of the code to run. For example, since the
    model training process may take a long time, users may only want to go through
    that process once. Setting the TRAIN_MODELS variable to 'False' tells the code
    to skip the training and use the trained models from a previous run.

    The code first goes through a 'Data Gathering' process, which reads in a lyrics
    file and optionally cleans it. Next, the code creates a lyrics embedding file
    (only relevant for GloVe and DistilBERT embedding strategies). Optionally, if
    not in full 'training' mode, the GloVe embeddings can be read-in from a pre-
    existing file.

    After the cleaned lyrics and embeddings are loaded, each of our 3 models (RNN,
    Homemade Transformer, and Fine-tuned GPT2) are (optionally) trained and then
    instantiated. A final section of the code (Custom Lyric Genre Prediction) loads
    example lyrics and demonstrates how the various model/embedding combinations
    can be used to predict genre.

    INSTALLATION:

    Step 1: Two large files must be downloaded separately, as they are too large to
    include in project package:
        a) First, from Kaggle.com, you need to download the main 'raw' lyric set
        called 'song_lyrics.csv' (~9 GB):
             i) https://www.kaggle.com/datasets/carlosgdcj/genius-song-lyrics-with-language-information?resource=download
            ii) This file should be placed in the /data directory, and the main.ipynb
                code will read this in and clean it, producing a 'song_lyrics_clean.csv'
                file used by the models.
        b) Second, from a Stanford University web site, you need to download a set
        of pre-trained word vectors called 'glove.42B.300d.zip' (~1.75 GB):
             i) https://nlp.stanford.edu/projects/glove/
            ii) This file should be unzipped, creating a file called 'glove.42B.300d.txt'
                which should be placed in the root 'model-training' package folder.

    Step 2: Create an Anaconda Python environment with the required dependencies per
    the pyenv.cfg and requirements.txt file

    EXECUTION:

    Step 1: Run the main.ipynb notebook:
        a) Choose your desired settings in the Global variables section
        b) We've included pre-trained models, so to save time you can set the
           'TRAIN_MODELS' variable to 'False'
            i) NOTE: you will need to run the "Data Gathering" section at least once
                     with TRAIN_MODELS set to 'True' and READ_CLEANED_OR_RAW set to
                     'RAW' in order to generate the 'song_lyrics_clean.csv' file
                     required for the visualization section.
        c) Even with pre-trained models, reading in GloVe embedding index will take
           ~45 seconds to load.

2) CODE\visualization

    DESCRIPTION:

    The visualization package is used to run a web application which allows a user to
    paste in any text-based set of lyrics, select their choice of embedding and model,
    and have the chosen model "Predict" which genre it feels the given lyrics belong to,
    along with a confidence percentage value representing how confident the model is
    in its prediction (relative to the other 5 genre choices).
    
    Optionally, the user can choose to "Predict Word by Word". In this case, the lyrics
    are run through the model multiple times - each time "leaving out" one of the words.
    In these cases, a genre prediction and confidence percentage is calculated for each
    run, and the difference in percentage is calculated based on the word left out of
    that specific run. This 'difference' is used as a measure of "contribution" that
    each word provides to the overall prediction (positive or negative).
    
    Visually, the text box is used to indicate the "state" of the lyrics at each stage of
    the process. When first pasted, one can see the lyrics in their original raw state.
    After hitting "Tokenize", the user can see how the lyrics were cleaned for processing.
    In the case of GloVe embedding, the text box will show which words were ignored - the
    so-called "stopwords" by depicting them in a lighter color. Finally, when "Predict Word
    by Word" is pressed, the words in the text box are color-coded based upon its calculated
    contribution to the overall prediction (i.e. the difference in overall confidence when
    that word is left out).

    There are two separate processes that need to run to support this visualization
    application:
        a) a Python/Flask-based REST API server that runs the models and does the predictions
        b) a web application (html, javascript, and supporting files)
    The actual visualization is handled via strategic use of the D3.js library. While the
    "text area" used for the lyrics *looks* like a standard HTML element, it is actually a
    combination of an SVG canvas superimposed upon a "hidden" standard text area. The hidden
    text area element is used soley as a target for the "paste" action. When the user "pastes"
    lyrics into the area on the page, the javascript code intercepts the action and redirects
    the block of text to the D3 engine, which renders each word as an individually stylized
    text object.

    INSTALLATION:

    Step 1: Prepare a Python environment with similar dependencies as described in the model-training
    section above (using the python.cfg and requirements.txt as a guide).
        a) in addition the the dependencies used for model training, the Flask package is
           required to be installed in order to run the Python REST API server
             i) pip install Flask
            ii) pip install Flask-CORS
        b) Copy the visualization folder to a location accessible by the Python environment

    Step 2: Use VS Code to open and run the web application
        a) Install VS Code extension "Five Server" (or similar) to provide a lightweight web
           server
        b) Open the \visualization folder insed VS Code

    Step 3: Copy the required large files over from the installation instructions provided in
    the "CODE\model_training" instructions from above:
        a) Copy the "song_lyrics_clean.csv" file that was generated in the main.ipynb code into
           the \data directory (NOTE: the "cleaned" version of the lyrics file is required here,
           so the "Data Gathering" section in the model_training\main.ipynb code must be ran at least once).
        b) Copy the "glove.42B.300d.txt" over into the root directory as-is

    EXECUTION:

    Step 1: Start the Python/Flask-based REST API server (see: https://realpython.com/api-integration-in-python/#flask)
        a) Launch a terminal window configured to use our Python environment
             i) this can be done via Anaconda or from within VS Code itself
            ii) navigate to the visualizations package folder if needed
        b) Within your terminal window, set the required FLASK environment variables
             i) MAC or Linux:
                - $ export FLASK_APP=app.py
                - $ export FLASK_ENV=development
            ii) Windows:
                - C:\> set FLASK_APP=app.py
                - C:\> set FLASK_ENV=development
        c) Launch the REST API server (app.py)
             i) $ flask run
            ii) (give it around 45 seconds to start up, as the GloVe embeddings need to load)

    Step 2: Launch the web page
        a) If using the Five Server VS Code extension: from within VS Code, right-click on the "index.html"
           file and select "Open with Five Server" to launch the main page
            i) or use whatever web server you prefer

    Step 3: Paste in any lyrics to any song (or make them up by typing into a text editor!)
        a) hint: Google "lyrics tennessee whiskey" or similar
        b) select the lyrics text and copy to the clipboard
        c) navigate back to the app page (index.html), right-click on the text area,
           and select "paste" to copy the lyrics download

    Step 4: Run your prediction, and visualize the results
        a) Select your Embedding strategy (GloVe or DistilBERT)
        b) Select your Model (Homemade Transformer, RNN, or GPT)
        c) Click "Tokenize" button to show cleaned lyrics
            i) if using GloVe embedding, notice the greyed-out "stopwords"
        d) Click the "Predict" button to see the overall genre prediction and relative confidence
        e) Click the "Predict Word by Word" button to see the color-coded "missing word" analysis
            i) Hover over any word to display a ToolTip showing the actual word contribution

    Step 5: Reset as desired
        a) Click the "Clear" button to clear out the lyrics, choose a different model, and run again
