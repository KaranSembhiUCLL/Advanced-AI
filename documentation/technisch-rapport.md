# Technisch rapport – AI Driver Monitoring System

## Inleiding

Voor dit project ontwikkelden we een AI-gebaseerd driver monitoring system dat met een webcam onveilig rijgedrag probeert te detecteren, met focus op vermoeidheidssignalen zoals gesloten ogen en geeuwen, en met de ambitie om ook afleiding zoals gsm-gebruik te herkennen.

De motivatie voor dit project is verkeersveiligheid: in taxi’s, vrachtwagens en delivery-voertuigen kan een waarschuwingssysteem nuttig zijn wanneer een bestuurder tekenen van slaperigheid of verminderde aandacht vertoont. In onze huidige implementatie ligt de nadruk vooral op drowsiness detection; gsm-detectie zat in het oorspronkelijke idee, maar is voor deze deadline nog niet als apart getraind detectiemodel afgewerkt.

De aanpak combineert computer vision en deep learning. We gebruikten OpenCV voor beeldverwerking, MediaPipe Face Landmarker voor gezichtsinformatie en hoofdoriëntatie, PyTorch voor de trainingspipeline en realtime inferentie, en twee aparte classificatiemodellen: één voor open/gesloten ogen en één voor geeuwen.

Repository-link: [https://github.com/KaranSembhiUCLL/Advanced-AI](https://github.com/KaranSembhiUCLL/Advanced-AI)

## Data

Voor het oogmodel werd een dataset met open en gesloten ogen gebruikt die uit parquet-bestanden werd ingeladen. In het notebook wordt een dataset van 126.560 voorbeelden vermeld, waarna de afbeeldingen werden uitgelezen, naar RGB omgezet, herschaald naar 224x224 pixels en lokaal gecachet om training sneller te maken. Voor het geeuwmodel werd een aparte yawn/no-yawn dataset gebruikt.

Voor de uitbreiding naar afleidingsdetectie (zoals mobiel telefoongebruik) onderzoeken we het gebruik van de [DMD-dataset](https://dmd.vicomtech.org). Dit is een omvangrijke dataset, wat een uitdaging vormt voor verwerking, maar waardevolle RGB-data biedt om gsm-detectie accuraat te trainen.

In de fine-tuningfase werd de dataset uitgebreid met 240 extra eigen beelden, waarna de totale dataset voor geeuwdetectie uitkwam op 5.359 voorbeelden, bijna gebalanceerd over beide klassen. **Belangrijk**: beide modellen werden gefinetuned met eigen ingebrachte data van onze eigen foto's, zodat ze beter aansluiten bij onze webcam- en belichtingsomstandigheden.

Beide pipelines gebruikten een train/validatieverdeling met stratificatie, zodat de labelverhouding behouden bleef. Belangrijke uitdagingen in de data waren variatie in belichting, camerahoek, kleine verschillen tussen personen, en het feit dat realtime webcambeelden vaak minder netjes zijn dan datasetbeelden.

## Model en methoden

Voor oogdetectie gebruikten we een zelfgebouwde CNN in PyTorch. Die bestaat uit drie convolutionele blokken met ReLU en max-pooling, gevolgd door flattening, een fully connected laag met dropout en een outputlaag voor twee klassen: open eyes en closed eyes.

Voor geeuwdetectie gebruikten we een krachtiger transfer learning-model op basis van EfficientNet-B0. De classifier werd aangepast naar een binaire output, waarna het model eerst werd getraind op de yawn/no-yawn dataset en nadien verfijnd met eigen beelden.

In de realtime predictor worden beide modellen gecombineerd. MediaPipe wordt gebruikt om gezichtslandmarks te extraheren, waarna oogregio’s en mondinformatie indirect bijdragen aan de beslissing; daarnaast worden ook heuristieken gebruikt zoals een yaw- en pitchdrempel voor hoofdpositie, smoothing over meerdere frames, stabiele framevensters en tijdsdrempels voordat een alarm wordt geactiveerd. De applicatie werkt als volgt: webcamframes worden ingelezen, gezichtslandmarks bepaald, oogmodel voorspelt ogen, geeuwmodel voorspelt yawn, en bij aanhoudende vermoeidheid volgt een waarschuwing.

## Resultaten en evaluatie

Het oogmodel behaalde sterke validatieresultaten. In het notebook staat voor het gefinetunede model een validatie-accuratesse tot ongeveer 0,9937, met train-accuratesse boven 0,999 tijdens de fine-tuningfase.

Ook het geeuwmodel presteerde goed. Na fine-tuning op extra eigen beelden werd een validatie-accuratesse van ongeveer 0,9804 tot 0,9795 gerapporteerd in de laatste epochs.

Deze resultaten tonen aan dat beide deelmodellen goed leren op hun respectieve datasets. Tegelijk blijft voorzichtigheid nodig: hoge validatie-accuratesse in een notebook betekent niet automatisch dat de volledige realtime toepassing even robuust is in alle omstandigheden, bijvoorbeeld bij slechte belichting, brillen, snelle hoofdbewegingen of gedeeltelijke occlusie.

## Bijdragen

Wij werkten samen aan het algemene concept van een AI driver monitoring system voor vermoeidheidsdetectie en afleiding. De uitgewerkte notebooks tonen concreet werk rond data-inladen, preprocessing, modeltraining, fine-tuning en realtime integratie van de twee modellen.

Concreet omvatte het technische werk onder meer: het inladen van datasets, het bouwen van een custom CNN voor oogdetectie, het trainen van een EfficientNet-B0 voor geeuwdetectie, het fine-tunen met eigen beelden en het samenbrengen van beide modellen in een predictor met webcam, MediaPipe en alarmen.

Online bronnen en libraries die duidelijk gebruikt werden zijn onder andere PyTorch, Torchvision, OpenCV en MediaPipe. GenAI werd in deze fase gebruikt om de bestaande losse informatie en notebooks te helpen structureren tot een kort en correct technisch rapport, zonder nieuwe projectinhoud te verzinnen; de feitelijke inhoud is gebaseerd op de aangeleverde notebooks en projectbeschrijving.

## Uitdagingen en toekomstig werk

De grootste uitdaging was dat het project nog niet volledig productierijp is en dat de documentatie tot nu toe beperkt en versnipperd was. Daarnaast was het vinden van geschikte datasets zeer moeilijk: veel datasets waren te klein of voldeden niet aan onze specifieke eisen voor driver monitoring. Het oorspronkelijke onderdeel rond gsm-detectie is nog niet uitgewerkt als afzonderlijk detectiemodel, waardoor de huidige focus in praktijk vooral op vermoeidheidsdetectie ligt.

Voor toekomstig werk willen we de repository beter structureren, de notebooks opschonen en de pipeline modulair maken. Op korte termijn willen we de huidige predictor verder tweaken voor een stabielere realtime prestatie. Voor de langere termijn kijken we naar de integratie van de DMD-dataset (Driver Monitoring Dataset - Vicomtech) om robuuste gsm-detectie toe te voegen, mits dit haalbaar blijkt gezien de omvang van deze dataset.

Daarnaast kan het systeem uitgebreid worden met meer testdata in realistische rijomstandigheden, extra evaluatiemaatstaven zoals precision/recall en een duidelijkere vergelijking tussen offline modelprestaties en realtime gedrag. Een volgende stap is het testen op meer personen, verschillende camera-opstellingen en moeilijkere omstandigheden zoals nachtbeelden, zonnebrillen en wisselende lichtinval. Op die manier kan het systeem betrouwbaarder en praktischer worden voor echte voertuigen.
