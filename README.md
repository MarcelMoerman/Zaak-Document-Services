# Generieke beschrijving architectuur ZDS en SIA platform

###1.1	Overall user story

Als burger van Amsterdam wil ik meldingen doen van geconstateerde overlast, defecten, en andere afwijkingen van de gewenste situatie in de openbare ruimte.

Zie <https://github.com/VNG-Realisatie/gemma-zaken/issues/39>

## 1.2	Toelichting op generieke userstory 

Amsterdam wil de meldingen openbare ruimte (signalen) als zaak registreren en zaaksgewijs behandelen zodat alle betrokkenen vanuit eenzelfde informatie positie kunnen worden voorzien van informatie. 

Het project in Amsterdam loopt parallel aan het ontwikkelen van de ZDS services en de Meldingen applicatie. Beoogde planning voor het realiseren van deze US is 25/7/2018.

## 1.3	Beknopte Procesbeschrijving

Onderstaande figuur toont het proces waar deze user story deel van uitmaakt: Behandelen Melding generiek.
 
* Indienen melding door de melder.
* Registreren van de melding door het ASC.
* Categoriseren van de melding door het ASC.
* Routering door ASC.
* Behandeling door proceseigenaar zaaktype.
* Afsluiting door proceseigenaar en ASC.
* Akkoord verklaring door melder.

>fig 1, Afbeelding Proces

## 1.4	Architectuurschets User Story

Meldingen over de openbare ruimte worden afgehandeld door het SCA met ondersteuning van het SIA platform.  
Meldingen kunnen een subcategorie bevatten die specifieke gegevens meegeeft aan het behandelingsproces. Voorbeeld hiervan is een melding overlast op het water waar specifieke gegevens over een rederij, bootnaam, nummer, type kunnen worden doorgegeven.
Het koppelen van dergelijke informatie aan een zaak_object is nu niet helder uitgewerkt in de ZDS en RGBZ, in dit traject zullen we hier een mogelijke oplossing voor introduceren.

Specifiek voor deze verwerking is het voor Amsterdam geitroduceerde SIA platform waarin meldingen worden verwerkt. Het SIA platform zorgt voor bestendiging, coördinatie en routering op de geregistreerde zaken. Primaire gerbuiker is het ASC (Actie Service Centrum) en eventueel afhandelende proceseigenaren van zaaktypen. 

Routering naar de betreffende afhandelende partijen kan via directe systeem koppelingen naar een taak-applicatie maar kan ook middels een mail of andere messaging platformen gebeuren.

Terugkoppeling op de afhandeling van de meldingen en bijv. statusupdates worden gecoordineerd op het SIA platform en geregistreerd in het ZRC, koppeling van een Taaksysteem op het ZRC is mogelijk op basis van StUF zaken of RESTFull API interfaces (in ontwikkeling).

Voor afhandeling door afdelingen zonder taak-applicatie is er een eenvoudige interface voor status updates in het SIA platform aanwezig.

>fig.2, Afbeelding Archimate proces en systemen

#### 1.4.1.	 Uitbreiding ZDS met taakspecifieke gegevens

Meldingen openbare ruimte kennen een onderverdeling in verschillende sub_categorien, zoals “meldingen overlast op water”.
Om de specifieke gegevens over een melding overlast op water te kunnen verwerken is er een set aan specifieke gegevens nodig (denk aan bootnaam, rederdij, nummer, registratie, …) deze set is niet onderdeel van een zaak zoals gedefinieerd binnen het RGBZ en de componenten.

Om deze gegevens wel mee te kunnen geven aan de behandelaar van de zaaktypen wordt er een toevoeging op gedaan op de ZTC en ZRC waarin een configureerbare  “ “Zaaktype_Specifieke_extensie” kan worden gedaan op het zaaktype.

De extensie beschrijft de opbouw van de “Taakspecifieke_Data” die meegegeven kan worden aan de behandelaar van het zaak_type.

>fig.3, Afbeelding systeem en gegevens

##1.5	Generieke architectuurschets (GEMMA-referentiecomponenten)

Deze user story volgt 2 GEMMA patronen voor Zaakgericht werken:
<https://www.gemmaonline.nl/index.php/ZGW_in_GEMMA_2_compleet#Indienen_productaanvraag_via_webformulier>  
en  
<https://www.gemmaonline.nl/index.php/ZGW_in_GEMMA_2_compleet#Registreren_zaak_vanuit_Zaakafhandelcomponent>
 
>Fig.4, GEMMA Mapping

|GEMMA comp.      |  J/N  | Verklaring                                        |  Akkoord  |
|:----------------|:-----:|:-------------------------------------------------|:---------:|
|E-Formulieren   |Ja|Het SIA platform voorziet in een formulieren omgeving voor invoer meldingen. |[x]|
|Servicebus    |Nee|Het SIA platform gebruikt de API’s van het Datapunt platform dit is geen "servicebus" maar een API platform.|[x]|
|ZRC	        |Ja|De meldingen worden zo snel mogelijk geregistreerd als Zaak om daarna te worden doorgezet naar de behandelaar, updates worden via de ZRC teruggekoppeld.     |[x]|
|ZTC          |Ja|De ZTC wordt geraadpleegd in een aantal van de processtapen van de afhnadeling van een melding. |[x]|
|ZDC| Nee|Er wordt (nog) geen ZDC gebruikt voor documenten, bijgeovoegde foto’s van een medling worden als object_data opgeslagen binnen het SIA platform.|[x]|
|Zaak afhandeling|Ja|De zaakafhandel component bestaat uit de taakapplicatie van de behandelaar.|[x]|
|Zaak coördinatie|Ja|Er wordt voor meldingen OR een specifieke coordinatie compnent ontwikkeld, doorontwikkeling naar een generieke component is nog niet besloten.|[x]|

## 1.6	Benodigde API’s in user story
Voor de realisatie van het SIA platform worden alle ZDS API’s gebruikt in logische volgorde worden de volgende API’s gebruikt:  
#### 1.6.1.	ZTC API
* Categoriseren van een melding OR, categorisering gebeurd in eerste instatie op basis van processing van de ingevoerde tekst en gegevens van de melding.  
* Routering van de meldingen op basis van de informatie over zaak_type en proces_eigenaar.  
* Anonieme terugkoppeling over verwachte afhendeltermijn door de proces_eigenaar.  

#### 1.6.2.	ZRC API
* Registratie van de meldingen als zaak.  
* Bewaking van de behandelingstermijn van de melding.  
* Registreren van status_updates door proces_eigenaar.  
* Anonieme terugkoppeling over meldingen.  

#### 1.6.3.	ZDC API
De ZDC wordt vooralsnog niet gebruikt voor afhandelingen OR, bijgevoegde foto’s worden nu als object_data met een URL en zaak_ID opgeslagen binnen het SIA platform.   

## 1.7	Eventuele onderliggende architectuurvragen

#### 1.7.1.	Architectuurvragen voor deze User Story
* Extensie op Zaaktype en Zaakregistratie voor taakspecifieke informatieoverdracht.  

#### 1.7.2.	GEMMA-onduidelijkheiden of afwijkingen
* Afwijking op patroon registratie in Taakapplicatie voor registratie in ZRC door directe registratie in ZRC.
