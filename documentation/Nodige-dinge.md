# Vermoeidheidsherkenning

## Benodigde technologie

Voor dit project zijn deze tools gebruikelijk:

- PyTorch voor het trainen van je model.
- OpenCV voor webcam-invoer en beeldverwerking.
- Gezichtsdetectie via bijvoorbeeld dlib of YOLO.
- Dataset met beelden van open en gesloten ogen, of een fatigue/drowsiness-dataset.

## Wat je kunt trainen

Een schoolvriendelijke modeltaak is:

- input: een afbeelding van een oog of gezicht;
- output: vermoeid of niet vermoeid.

Als je wat verder wilt gaan, kun je meerdere signalen combineren, zoals:

- ogen dicht;
- gezichten
- langzaam knipperen.
- hoofd naar beneden? dan is het te laat --> Alarm

# Mogelijke datasets

https://huggingface.co/datasets/MichalMlodawski/closed-open-eyes  
https://www.kaggle.com/datasets/davidvazquezcic/yawn-dataset

The RGB distraction of this one
https://dmd.vicomtech.org

pip install -r requirements.txt
