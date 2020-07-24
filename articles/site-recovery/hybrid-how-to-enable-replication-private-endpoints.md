---
title: Aktivera replikering för lokala datorer med privata slut punkter i Azure Site Recovery
description: Den här artikeln beskriver hur du konfigurerar replikering för lokala datorer med privata slut punkter med hjälp av Site Recovery.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: c91c92a18570f569b6364b646e6fdf7bf534802f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100144"
---
# <a name="replicate-on-premises-machines-with-private-endpoints"></a>Replikera lokala datorer med privata slut punkter

Med Azure Site Recovery kan du använda privata [Azure Private Link](../private-link/private-endpoint-overview.md) -slutpunkter för att replikera dina lokala datorer till ett virtuellt nätverk i Azure. Stöd för åtkomst till privata slut punkter till ett återställnings valv stöds i följande regioner:

- Azure Commercial: södra centrala USA, västra USA 2, östra USA
- Azure Government: US Gov, Virginia, US Gov, Arizona, US Gov, Texas, US DoD, östra, US DoD, centrala

Den här artikeln innehåller anvisningar för att utföra följande steg:

- Skapa ett Azure Backup Recovery Services valv för att skydda dina datorer.
- Aktivera en hanterad identitet för valvet och ge de behörigheter som krävs för att få åtkomst till kundens lagrings konton för att replikera trafik från lokala platser till Azures mål platser. Hanterad identitets åtkomst för lagring krävs när du konfigurerar privat länk åtkomst till valvet.
- Gör DNS-ändringar nödvändiga för privata slut punkter
- Skapa och godkänn privata slut punkter för ett valv i ett virtuellt nätverk
- Skapa privata slut punkter för lagrings kontona. Du kan fortsätta att tillåta offentlig eller brand Väggs åtkomst för lagring vid behov. Det är inte obligatoriskt att skapa en privat slut punkt för åtkomst till lagrings utrymme för Azure Site Recovery.
  
Nedan visas en referens arkitektur för hur arbets flödet för replikering ändras för Hybrid haveri beredskap med privata slut punkter. Det går inte att skapa privata slut punkter i ditt lokala nätverk. För att kunna använda privata länkar måste du skapa ett virtuellt Azure-nätverk (kallas "kringgå nätverk" i den här artikeln), upprätta en privat anslutning mellan lokalt och det kringgåde nätverket och sedan skapa privata slut punkter i det kringgåde nätverket. Valet av privat anslutning är på egen hand.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Referens arkitektur för Site Recovery med privata slut punkter.":::

## <a name="prerequisites-and-caveats"></a>Krav och varningar

- Stöd för privata länkar är aktiverat för Site Recovery infrastruktur med **9,35** -versionen och senare.
- Privata slut punkter kan bara skapas för nya Recovery Services-valv som inte har några objekt registrerade för valvet. Därför måste privata slut punkter **skapas innan objekt läggs till i valvet**. Granska pris strukturen för [privata slut punkter](https://azure.microsoft.com/pricing/details/private-link/).
- När en privat slut punkt skapas för ett valv är valvet låst och **kan inte nås från andra nätverk än de nätverk som har privata slut punkter**.
- Azure Active Directory stöder för närvarande inte privata slut punkter. Därför måste IP-adresser och fullständigt kvalificerade domän namn som krävs för att Azure Active Directory ska fungera i en region tillåtas utgående åtkomst från det skyddade virtuella Azure-nätverket. Du kan också använda nätverks säkerhets grupp tag gen "Azure Active Directory" och Azure Firewall-taggar för att tillåta åtkomst till Azure Active Directory, efter vad som är tillämpligt.
- **Fem IP-adresser krävs** i det kringgåde nätverket där du skapar din privata slut punkt. När du skapar en privat slut punkt för valvet skapar Site Recovery fem privata Länkar för åtkomst till dess mikrotjänster.
- **En ytterligare IP-adress krävs** i kringgå nätverk för anslutning till ett lagrings konto för privata slut punkter. Anslutnings metoden, till exempel Internet eller [ExpressRoute](../expressroute/index.yml), mellan lokalt och din lagrings konto slut punkt finns i beskrivningen. Att upprätta en privat länk är valfritt. Det går bara att skapa privata slut punkter för lagring på Generell användning v2-typ. Granska pris strukturen för [data överföring på GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Skapa och använda privata slut punkter för Site Recovery

 Det här avsnittet beskriver de steg som ingår i att skapa och använda privata slut punkter för Azure Site Recovery i dina virtuella nätverk.

> [!NOTE]
> Vi rekommenderar starkt att du följer de här stegen i samma ordning som anges. Om du inte gör det kan det leda till att valvet återges att det inte går att använda privata slut punkter och kräver att du startar om processen med ett nytt valv.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv.

Ett Recovery Services-valv är en entitet som innehåller replikeringsinformation för datorer och används för att utlösa Site Recovery åtgärder. Anvisningar för hur du skapar ett Recovery Services-valv i den Azure-region där du vill redundansväxla, om det finns en katastrof, finns i [skapa ett Recovery Services valv](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Aktivera den hanterade identiteten för valvet.

Med en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) kan valvet få åtkomst till kundens lagrings konton. Site Recovery behöver åtkomst till lagrings-och cache-/logg lagrings konton beroende på scenario kravet. Hanterad identitets åtkomst är nödvändig när du använder privata länk tjänster för valvet.

1. Gå till Recovery Services-valvet. Välj **identitet** under _Inställningar_.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Visar Azure Portal och Recovery Services sidan.":::

1. Ändra **statusen** till _på_ och välj **Spara**.

1. Ett **objekt-ID** genereras som anger att valvet nu har registrerats med Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Skapa privata slut punkter för Recovery Services valvet

Du behöver en privat slut punkt för valvet i bypass-nätverket för att skydda datorer i det lokala käll nätverket. Skapa den privata slut punkten med hjälp av det privata länk centret i portalen eller via [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Sök efter och välj "privat länk" i Sök fältet Azure Portal. Den här åtgärden tar dig till det privata länk centret.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Visar hur du söker i Azure Portal för det privata länk centret.":::

1. I det vänstra navigerings fältet väljer du **privata slut punkter**. På sidan privata slut punkter väljer du ** \+ Lägg till** för att börja skapa en privat slut punkt för valvet.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Visar hur du skapar en privat slut punkt i det privata länk centret.":::

1. När du är i "skapa privat slut punkt" måste du ange information om hur du skapar din privata slut punkts anslutning.

   1. **Grundläggande**information: Fyll i den grundläggande informationen för dina privata slut punkter. Regionen ska vara samma som det kringgåde nätverket.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Visar fliken grundläggande, projekt information, prenumeration och andra relaterade fält för att skapa en privat slut punkt i Azure Portal.":::

   1. **Resurs**: den här fliken kräver att du nämner den plattform-som-tjänst-resurs som du vill skapa anslutningen för. Välj _Microsoft. RecoveryServices/valv_ från **resurs typen** för den valda prenumerationen. Välj sedan namnet på Recovery Services valvet för **resurs** och ange _Azure Site Recovery_ som **mål under resurs**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Visar fälten resurs, resurs typ, resurs och mål under resurs för länkning till en privat slut punkt i Azure Portal.":::

   1. **Konfiguration**: i konfiguration anger du det kringgående-nätverk och undernät där du vill att den privata slut punkten ska skapas. Aktivera integrering med privat DNS-zon genom att välja **Ja**.
      Välj en DNS-zon som redan har skapats eller skapa en ny. Om du väljer **Ja** länkas zonen automatiskt till Bypass-nätverket och de DNS-poster som krävs för DNS-matchning för nya IP-adresser och fullständiga domän namn som skapats för den privata slut punkten läggs till.

      Se till att du väljer att skapa en ny DNS-zon för varje ny privat slut punkt som ansluter till samma valv. Om du väljer en befintlig privat DNS-zon skrivs de tidigare CNAME-posterna över. Se [vägledning för privat slut punkt](../private-link/private-endpoint-overview.md#private-endpoint-properties) innan du fortsätter.

      Om din miljö har en nav-och eker-modell behöver du bara en privat slut punkt och bara en privat DNS-zon för hela installationen eftersom alla dina virtuella nätverk redan har peering aktiverat mellan dem. Mer information finns i [DNS-integrering för privata slut punkter](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Om du vill skapa en privat DNS-zon manuellt följer du stegen i [skapa privata DNS-zoner och lägger till DNS-poster manuellt](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Visar fliken Konfiguration med fälten nätverk och DNS-integrering för konfiguration av en privat slut punkt i Azure Portal.":::

   1. **Taggar**: om du vill kan du lägga till taggar för din privata slut punkt.

   1. **Granska \+ skapa**: När verifieringen är klar väljer du **skapa** för att skapa den privata slut punkten.

När den privata slut punkten har skapats läggs fem fullständigt kvalificerade domän namn till i den privata slut punkten. Dessa länkar gör att datorerna i det lokala nätverket kan komma åt via kringgå nätverket till alla nödvändiga Site Recovery mikrotjänster i kontexten för valvet. Samma privata slut punkt kan användas för att skydda en Azure-dator i bypass-nätverket och alla peer-peer-nätverk.

De fem domän namnen är formaterade med följande mönster:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Godkänn privata slut punkter för Site Recovery

Om användaren som skapar den privata slut punkten också är ägare av Recovery Services-valvet, godkänns den privata slut punkten som skapades ovan automatiskt inom några minuter. Annars måste ägaren av valvet godkänna den privata slut punkten innan du kan använda den. Om du vill godkänna eller avvisa en begärd privat slut punkts anslutning går du till **anslutningar för privata slut punkter** under "Inställningar" på sidan återställnings valv.

Du kan gå till den privata slut punkts resursen för att granska status för anslutningen innan du fortsätter.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Visar sidan anslutningar för privata slut punkter i valvet och listan över anslutningar i Azure Portal.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Valfritt Skapa privata slut punkter för lagrings kontot för cache

En privat slut punkt till Azure Storage kan användas. Att skapa privata slut punkter för lagrings åtkomst är _valfritt_ för Azure Site Recovery replikering. När du skapar en privat slut punkt för lagring behöver du en privat slut punkt för cache-/logg lagrings kontot i ditt kringgå virtuella nätverk.

> [!NOTE]
> Det går bara att skapa en privat slut punkt för lagring på ett **generell användning v2** -lagrings konton. För pris information, se [standard Page BLOB-priser](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Följ [rikt linjerna för att skapa privat lagring](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) för att skapa ett lagrings konto med privat slut punkt. Se till att välja **Ja** för integrering med privat DNS-zon. Välj en DNS-zon som redan har skapats eller skapa en ny.

## <a name="grant-required-permissions-to-the-vault"></a>Bevilja de behörigheter som krävs för valvet

Beroende på din konfiguration kan du behöva ett eller flera lagrings konton i Azures mål Azure-region. Ge sedan hanterade identitets behörigheter för alla cache-/logg lagrings konton som krävs av Site Recovery. I så fall måste du skapa de lagrings konton som krävs i förväg.

Innan du aktiverar replikering av virtuella datorer måste den hanterade identiteten för valvet ha följande roll behörigheter beroende på typ av lagrings konto:

- Resource Manager-baserade lagrings konton (standard typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage BLOB data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager-baserade lagrings konton (Premium typ):
  - [Deltagare](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage BLOB data-ägare](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klassiska lagrings konton:
  - [Klassisk lagrings konto deltagare](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klassisk lagrings kontots nyckel operatörs tjänst roll](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Följande steg beskriver hur du lägger till en roll tilldelning till dina lagrings konton, en i taget:

1. Gå till lagrings kontot och navigera till **åtkomst kontroll (IAM)** till vänster på sidan.

1. När du har aktiverat **åtkomst kontroll (IAM)** i rutan Lägg till en roll tilldelning väljer du **Lägg till**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Visar sidan åtkomst kontroll (IAM) på ett lagrings konto och knappen Lägg till en roll tilldelning i Azure Portal.":::

1. På sidan Lägg till en roll tilldelning väljer du rollen i listan ovan i list rutan **roll** . Ange **namnet** på valvet och välj **Spara**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Visar sidan åtkomst kontroll (IAM) på ett lagrings konto och alternativen för att välja en roll och vilket huvud konto som ska ge rollen till i Azure Portal.":::

Utöver dessa behörigheter måste även MS-betrodda tjänster beviljas åtkomst. Gå till "brand väggar och virtuella nätverk" och markera kryss rutan Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot i **undantag**.

## <a name="protect-your-virtual-machines"></a>Skydda dina virtuella datorer

När alla konfigurationer ovan har slutförts fortsätter du med installationen av din lokala infrastruktur.

- [Distribuera konfigurations servern för VMware och fysiska datorer](./vmware-azure-deploy-configuration-server.md)
- ELLER [Konfigurera Hyper-V-miljön för replikering](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

När installationen är klar aktiverar du replikeringen för käll datorerna. Kontrol lera att konfigurationen av infrastrukturen utförs först när de privata slut punkterna för valvet redan har skapats i det kringgåde nätverket.

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Skapa privata DNS-zoner och Lägg till DNS-poster manuellt

Om du inte har valt alternativet att integrera med en privat DNS-zon när du skapade privat slut punkt för valvet följer du stegen i det här avsnittet.

Skapa en privat DNS-zon som tillåter Site Recovery-providern (för Hyper-V-datorer) eller processervern (för VMware/fysiska datorer) för att matcha privata länkar fullständigt kvalificerade domän namn till privata IP-adresser.

1. Skapa en privat DNS-zon

   1. Sök efter "Privat DNS zon" i Sök fältet **alla tjänster** och välj "privat DNS zoner" i list rutan.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Visar sökning efter "privat DNS-zon" på nya resurser på sidan Azure Portal.":::

   1. På sidan Privat DNS zoner väljer du knappen ** \+ Lägg till** för att börja skapa en ny zon.

   1. Fyll i den information som krävs på sidan Skapa privat DNS-zon. Ange namnet på den privata DNS-zonen som `privatelink.siterecovery.windowsazure.com` . Du kan välja vilken resurs grupp och vilken prenumeration som helst för att skapa den.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Visar fliken grundläggande på sidan Skapa Privat DNS zon och relaterad projekt information i Azure Portal.":::

   1. Fortsätt till fliken **Granska \+ skapande** för att granska och skapa DNS-zonen.

1. Länka en privat DNS-zon till ditt virtuella nätverk

   Den privata DNS-zon som skapades ovan måste nu länkas till Bypass.

   1. Gå till den privata DNS-zon som du skapade i föregående steg och navigera till **virtuella nätverks länkar** till vänster på sidan. Sedan väljer du knappen ** \+ Lägg till** .

   1. Fyll i den information som krävs. Fälten **prenumeration** och **virtuell nätverk** måste fyllas i med motsvarande information om bypass-nätverket. De andra fälten måste vara kvar som de är.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Visar sidan för att lägga till en virtuell nätverks länk med länk namnet, prenumerationen och det relaterade virtuella nätverket i Azure Portal.":::

1. Lägg till DNS-poster

   När du har skapat den nödvändiga privata DNS-zonen och den privata slut punkten måste du lägga till DNS-poster i din DNS-zon.

   > [!NOTE]
   > Om du använder en anpassad privat DNS-zon ser du till att liknande poster görs enligt beskrivningen nedan.

   Det här steget kräver att du skapar poster för varje fullständigt kvalificerat domän namn i din privata slut punkt i din privata DNS-zon.

   1. Gå till din privata DNS-zon och gå till **översikts** avsnittet till vänster på sidan. När så är fallet väljer du ** \+ post uppsättning** för att börja lägga till poster.

   1. På sidan Lägg till post uppsättning som öppnas lägger du till en post för varje fullständigt kvalificerat domän namn och privat IP-adress som _en_ typ post. Listan över fullständigt kvalificerade domän namn och IP-adresser kan hämtas från sidan "privat slut punkt" i **Översikt**. Som du ser i exemplet nedan läggs det första fullständigt kvalificerade domän namnet från den privata slut punkten till i post uppsättningen i den privata DNS-zonen.

      Dessa fullständigt kvalificerade domän namn matchar mönstret:`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Visar sidan för att lägga till en DNS A-posttyp för det fullständigt kvalificerade domän namnet till den privata slut punkten i Azure Portal.":::

## <a name="next-steps"></a>Nästa steg

Nu när du har aktiverat privata slut punkter för replikeringen av virtuella datorer kan du se följande sidor för ytterligare och relaterad information:

- [Distribuera en lokal konfigurations Server](./vmware-azure-deploy-configuration-server.md)
- [Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure](./hyper-v-azure-tutorial.md)