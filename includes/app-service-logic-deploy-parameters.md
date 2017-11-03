Du kan definiera parametrar för värdena som ska användas när du distribuerar mallen med Azure Resource Manager. Mallen innehåller en `parameters` avsnitt som innehåller alla parametervärden. Varje parametervärdet används av mallen för att definiera de resurser som du vill distribuera.

> [!NOTE]
> Definiera inte parametrar för värden som aldrig ändras. Definiera parametrar endast för värden som varierar baserat på projektet som du distribuerar eller baserat på miljön där du distribuerar.

När du definierar parametrar:

* Ange de tillåtna värdena som en användare kan ange under distributionen av **allowedValues** fältet.

* Om du vill tilldela värden till parametern när inga värden anges under distributionen, Använd den **defaultValue** fältet. 
