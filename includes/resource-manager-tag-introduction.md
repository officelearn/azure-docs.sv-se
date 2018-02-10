Du kan ordna Azure-resurser i kategorier genom att lägga till taggar. Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön. Utan taggen kan det vara svårt att identifiera om en resurs är avsedd för utveckling, testning eller produktion. "Miljö" och "produktion" är bara ett exempel. Du anger de namn och värden som passar bäst för din prenumeration.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Taggarna gör att du kan hämta relaterade resurser som finns i olika resursgrupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar. Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. Den här artikeln innehåller ett exempel som illustrerar hur du tilldelar en JSON-sträng till taggen.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Taggar kan inte tillämpas på klassiska resurser, till exempel ”tjänst i molnet (klassiskt)”.
* Dessa tecken stöds inte:
  * `<`
  * `>`
  * `%`
  * `&`
  * `\\`
  * `?`
  * `/`
