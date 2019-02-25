## <a name="updating-properties-on-an-existing-cloud-spatial-anchor"></a>Uppdatera egenskaperna på en befintlig cloud spatial fästpunkt

Om du vill uppdatera egenskaperna på en fästpunkt, kan du använda metoden UpdateAnchorPropertiesAsync. Om två eller flera enheter försöker uppdatera egenskaper för samma förtroendeankare på samma gång, använder vi en modell för Optimistisk samtidighet. Vilket innebär att den första skrivningen ska ha.  Alla andra skrivningar får ett ”Samtidighetsfel”: en uppdatering av egenskaperna skulle behövas innan du försöker igen.
