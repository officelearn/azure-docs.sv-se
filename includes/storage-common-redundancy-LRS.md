Lokalt redundant lagring (LRS) är utformad att ge minst 99.999999999% (11 9's) hållbarhet objekt under ett visst år genom att replikera data inom en lagringsenhet för skalan som finns i ett datacenter i den region där du skapade ditt lagringskonto. En begäran om skrivning returnerar har endast när den har skrivits till alla repliker. De här replikeringarna finns i separata feldomäner och uppgradera domäner inom en lagringsenhet för skalan.

En skalningsenhet för lagring är en samling av rack lagringsnoder. En feldomän (FD) är en uppsättning noder som representerar en fysisk enhet till felet och kan anses noder som tillhör samma fysiska rack. En uppgraderingsdomän (UD) är en uppsättning noder som uppgraderas tillsammans under service-uppgraderingen (distribution). Replikerna sprids över UDs och FDs inom en skala lagringsenhet så att data är tillgängliga även om maskinvarufel påverkar ett enda rack eller när noder uppgraderas under en distribution.

LRS är alternativet lägsta kostnad och erbjuder minst hållbarhet jämfört med andra alternativ. Alla repliker kan vara förlorade eller ett oåterkalleligt händelse av en nivå katastrofåterställning för datacenter (brand, överbelasta osv). Geo-Redundant lagring (GRS) rekommenderas för de flesta program för att minska denna risk.

Lokalt redundant lagring kan fortfarande vara önskvärt i vissa scenarier:

* Ger högsta Maximal bandbredd för alternativ för Azure Storage-replikering.
* Om ditt program lagrar data som enkelt kan rekonstrueras, kan du välja LRS.
* Vissa program är begränsade till att replikera data bara inom ett land på grund av kraven för styrning. En parad region kan vara i ett annat land. Läs mer på region par [Azure-regioner](https://azure.microsoft.com/regions/).
