## <a name="locating-a-cloud-spatial-anchor"></a>Hitta en spatial ankare för molnet

För att hitta spatial molnankare måste veta deras identifierare. Ankar-ID: N kan vara lagrad i dina program s backend-tjänsten och tillgänglig för alla enheter som korrekt kan autentisera till den. Ett exempel på detta finns i [självstudien: Dela Spatial ankare över enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Skapa en instans av ett AnchorLocateCriteria objekt genom att ange de identifierare som du letar efter och anropa metoden CreateWatcher för sessionen genom att tillhandahålla ditt AnchorLocateCriteria.
