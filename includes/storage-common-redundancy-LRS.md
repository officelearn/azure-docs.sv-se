Lokalt redundant lagring (LRS) replikeras dina data tre gånger i en lagringsenhet för skalan som finns i ett datacenter i den region där du skapade ditt lagringskonto. En Skriv-begäran returnerar har endast när den har skrivits till alla tre repliker. Dessa tre repliker finns i separata feldomäner och uppgradera domäner inom en lagringsenhet för skalan.

En skalningsenhet för lagring är en samling av rack lagringsnoder. En feldomän (FD) är en uppsättning noder som representerar en fysisk enhet till felet och kan anses noder som tillhör samma fysiska rack. En uppgraderingsdomän (UD) är en uppsättning noder som uppgraderas tillsammans under service-uppgraderingen (distribution). De tre replikerna sprids över UDs och FDs inom en skala lagringsenhet så att data är tillgängliga även om maskinvarufel påverkar ett enda rack eller när noder uppgraderas under en distribution.

LRS är alternativet lägsta kostnad och erbjuder minst hållbarhet jämfört med andra alternativ. Vid en nivå katastrofåterställning för datacenter (brand, överbelasta osv) kanske alla tre repliker förlorade eller oåterkalleligt. Geo-Redundant lagring (GRS) rekommenderas för de flesta program för att minska denna risk.

Lokalt redundant lagring kan fortfarande vara önskvärt i vissa scenarier:

* Ger högsta Maximal bandbredd för alternativ för Azure Storage-replikering.
* Om ditt program lagrar data som enkelt kan rekonstrueras, kan du välja LRS.
* Vissa program är begränsade till att replikera data bara inom ett land på grund av kraven för styrning. En parad region kan vara i ett annat land. Läs mer på region par [Azure-regioner](https://azure.microsoft.com/regions/).