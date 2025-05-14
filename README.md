# Datawarehouse query's uitvoeren in Microsoft Fabric

## Overzicht
Deze handleiding beschrijft SQL-query's en stappen voor het effectief ophalen, verifiëren en beheren van gegevens in het **sample-dw**-datawarehouse in **Microsoft Fabric**. De query's helpen bij het analyseren van reisgegevens, het controleren op inconsistenties en het maken van gefilterde weergaven.

## Vereisten
- Toegang tot **Microsoft Fabric** met een proefversie of een gelicentieerde werkruimte.
- Een **sample-dw**-datawarehouse aangemaakt.
- Basiskennis van **Transact-SQL (T-SQL)**.

## Datawarehouse query's uitvoeren
### Totaal aantal reizen en omzet per maand ophalen

1-Voer de volgende query uit om het totale aantal reizen en de omzet per maand op te halen.

```sql
SELECT
D.MaandNaam,
COUNT(*) AS TotalTrips,
SUM(T.TotaalBedrag) AS TotaleOmzet
FROM dbo.Reis AS T
JOIN dbo.[Datum] AS D
ON T.[DatumID] = D.[DatumID]
GROUP BY D.MaandNaam;


2-Gemiddelde reisduur en -afstand per dag
Voer de volgende query uit om reizen per dag van de week te analyseren.
SELECT 
    D.DayName, 
    AVG(T.TripDurationSeconds) AS AvgDuration, 
    AVG(T.TripDistanceMiles) AS AvgDistance 
FROM dbo.Trip AS T
JOIN dbo.[Date] AS D
    ON T.[DateID] = D.[DateID]
GROUP BY D.DayName;


3-Top 10 ophaal- en afleverlocaties
Vind de populairste ophaal- en afleverlocaties.
SELECT TOP 10 
    G.City, 
    COUNT(*) AS TotalTrips 
FROM dbo.Trip AS T
JOIN dbo.Geography AS G
    ON T.PickupGeographyID = G.GeographyID
GROUP BY G.City
ORDER BY TotalTrips DESC;


4-Verificatie van gegevensconsistentie
Controleer op reizen met een ongebruikelijk lange duur
Controleer of reizen langer dan 24 uur duren.
SELECT COUNT(*) 
FROM dbo.Trip 
WHERE TripDurationSeconds > 86400;



5-Controleer en verwijder negatieve reisduren
Identificeer en verwijder reizen met ongeldige duurwaarden.
SELECT COUNT(*) 
FROM dbo.Trip 
WHERE TripDurationSeconds < 0;

DELETE FROM dbo.Trip 
WHERE TripDurationSeconds < 0;


6-Opslaan als weergave
Een gefilterde weergave maken voor reizen in januari
Genereer een weergave die reizen filtert zodat alleen die uit januari worden opgenomen.
SELECT 
    D.DayName, 
    AVG(T.TripDurationSeconds) AS AvgDuration, 
    AVG(T.TripDistanceMiles) AS AvgDistance 
FROM dbo.Trip AS T
JOIN dbo.[Date] AS D
    ON T.[DateID] = D.[DateID]
WHERE D.Month = 1
GROUP BY D.DayName;

# Query-a-data-warehouse-in-Microsoft-Fabric
