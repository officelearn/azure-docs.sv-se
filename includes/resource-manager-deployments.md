## <a name="incremental-and-complete-deployments"></a>Inkrementella och fullständiga distributioner
När du distribuerar dina resurser kan ange du att distributionen är en inkrementell uppdatering eller en fullständig uppdatering. Den främsta skillnaden mellan dessa två lägen är hur Resource Manager hanterar befintliga resurser i resursgruppen som inte ingår i mallen:

* I Resource Manager-fullständig läge **tar bort** resurser som finns i resursgruppen men har inte angetts i mallen. 
* I Resource Manager-inkrementell läge **lämnar oförändrat** resurser som finns i resursgruppen men har inte angetts i mallen.

Resource Manager försöker etablera alla resurser som har angetts i mallen för båda lägena. Åtgärden resulterar i ingen ändring om resursen finns redan i resursgruppen och dess inställningar har inte ändrats. Om du ändrar inställningarna för en resurs kan etableras resursen med de nya inställningarna. Om du försöker uppdatera platsen eller typ av en befintlig resurs misslyckas distributionen med ett fel. I stället distribuerar en ny resurs med platsen eller ange att du behöver.

Som standard använder Resource Manager inkrementell läge.

Studera följande scenario för att illustrera skillnaden mellan inkrementella och fullständiga lägen.

**Befintlig resursgrupp** innehåller:

* Resurs A
* Resurs B
* Resurs C

**Mallen** definierar:

* Resurs A
* Resurs B
* Resurs D

När de distribueras i **inkrementella** läge, resursgruppen innehåller:

* Resurs A
* Resurs B
* Resurs C
* Resurs D

När de distribueras i **fullständig** resurs C-läge har tagits bort. Resursgruppen innehåller:

* Resurs A
* Resurs B
* Resurs D
