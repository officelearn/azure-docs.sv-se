---
title: Distribuera Azure Databricks i ditt virtuella nätverk (förhandsversion)
description: Den här artikeln beskriver hur du distribuerar Azure Databricks till ditt virtuella nätverk, även kallat VNet-inmatning.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003453"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Distribuera Azure Databricks i ditt virtuella nätverk (förhandsversion)

Standarddistribution av Azure Databricks är en fullständigt hanterad tjänst på Azure: alla data plan resurser, inklusive ett virtuellt nätverk (VNet) har distribuerats till en låst resursgrupp. Om du behöver nätverk anpassning, men du kan distribuera Azure Databricks-resurser i ditt eget virtuella nätverk (även kallade VNet-inmatning), när kan du:

* Anslut Azure Databricks till andra Azure-tjänster (till exempel Azure Storage) på ett säkrare sätt med hjälp av tjänstslutpunkter.
* Anslut till lokala data källor för användning med Azure Databricks, genom att dra nytta av användardefinierade vägar.
* Ansluta Azure Databricks till en virtuell nätverksinstallation kan granska all utgående trafik och vidta åtgärder enligt tillåta och neka regler.
* Konfigurera Azure Databricks för att använda anpassad DNS.
* Konfigurera regler för nätverkssäkerhetsgrupper (NSG) om du vill ange begränsningar för utgående trafik.
* Distribuera Azure Databricks-kluster i din befintliga virtuella nätverket.

Distribuera Azure Databricks resurser till ditt eget virtuella nätverk kan du dra nytta av flexibla CIDR-intervall (var som helst mellan /16- / 24 för det virtuella nätverket och mellan /18- / 26 för undernät).

  > [!NOTE]
  > Du kan inte ersätta det virtuella nätverket för en befintlig arbetsyta. Om din aktuella arbetsyta inte kan hantera det begärda antalet aktiva klusternoder, kan du skapa en annan arbetsyta i ett större virtuellt nätverk. Följ [dessa detaljerad migreringssteg](howto-regional-disaster-recovery.md#detailed-migration-steps) att kopiera resurser (anteckningsböcker, klusterkonfigurationer, jobb) från gammalt till ny arbetsyta.

## <a name="virtual-network-requirements"></a>Krav för virtuellt nätverk

Du kan använda gränssnittet Azure Databricks-arbetsyta distribution i Azure-portalen för att automatiskt konfigurera ett befintligt virtuellt nätverk med den nödvändiga undernät och nätverkssäkerhetsgrupp inställningar för lista över tillåtna eller du kan använda Azure Resource Manager mallar för att konfigurera ditt virtuella nätverk och distribuera din arbetsyta.

Det virtuella nätverket som du distribuerar Azure Databricks-arbetsytan till måste uppfylla följande krav:

### <a name="location"></a>Location

Det virtuella nätverket måste finnas på samma plats som Azure Databricks-arbetsytan.

### <a name="subnets"></a>Undernät

Det virtuella nätverket måste innehålla två undernät som är dedikerad till Azure Databricks:

   1. Ett privat undernät med en konfigurerad nätverkssäkerhetsgrupp som tillåter kluster-intern kommunikation

   2. En offentlig undernät med en konfigurerad nätverkssäkerhetsgrupp som tillåter kommunikation med Azure Databricks kontrollplanet.

### <a name="address-space"></a>Adressutrymme

CIDR-block mellan /16/24 för det virtuella nätverket och en CIDR-block mellan /18-/26 för de privata och offentliga undernäten.

### <a name="whitelisting"></a>Godkännande

All utgående och inkommande trafik mellan undernät och kontrollplanet Azure Databricks måste vara godkänd.

## <a name="create-an-azure-databricks-workspace"></a>Skapa en Azure Databricks-arbetsyta

Det här avsnittet beskriver hur du skapar en Azure Databricks-arbetsyta i Azure-portalen och distribuerar den i din egen befintliga virtuella nätverket. Azure Databricks uppdaterar det virtuella nätverket med två nya undernät och nätverkssäkerhetsgrupper med hjälp av CIDR-intervall som tillhandahålls av dig, vitlistor inkommande och utgående undernätstrafik är och distribuerar arbetsytan till det uppdaterade virtuella nätverket.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste ha ett virtuellt nätverk som du distribuerar Azure Databricks-arbetsytan. Du kan använda ett befintligt virtuellt nätverk eller skapa en ny, men det virtuella nätverket måste vara i samma region som Azure Databricks-arbetsytan som du planerar att skapa. CIDR-intervall mellan /16 /24 krävs för det virtuella nätverket.

  > [!Warning]
  > En arbetsyta med ett mindre virtuellt nätverk – det vill säga en lägre CIDR-intervall – kan få slut på IP-adresser (nätverksutrymme) snabbare än en arbetsyta med ett större virtuellt nätverk. Till exempel en arbetsyta med ett/24-nätverk och /26 undernät kan ha maximalt 64 noder som är aktiva samtidigt, medan en arbetsyta med en /20 virtuellt nätverk och /22 undernät kan innehålla högst 1024 noder.

  Dina undernät skapas automatiskt när du konfigurerar din arbetsyta och har möjlighet att tillhandahålla CIDR-intervall för undernät under konfigurationen.

## <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

1. I Azure-portalen väljer du **+ skapa en resurs > Analytics > Azure Databricks** att öppna dialogrutan för Azure Databricks-tjänst.

2. Följ konfigurationsstegen som beskrivs i steg 2: Skapa en Azure Databricks-arbetsyta i komma igång-guiden och välj distribuera Azure Databricks-arbetsytan i ditt virtuella nätverk-alternativ.

   ![Skapa Azure Databricks-tjänst](./media/vnet-injection/create-databricks-service.png)

3. Välj det virtuella nätverket som du vill använda.

   ![Alternativ för virtuellt nätverk](./media/vnet-injection/select-vnet.png)

4. Ange CIDR-intervall i ett block mellan /18-/26 för två undernät, som är dedikerad till Azure Databricks:

   * En offentlig undernät kommer att skapas med en nätverkssäkerhetsgrupp som tillåter kommunikation med Azure Databricks kontrollplanet.
   * Ett privat undernät kommer att skapas med en nätverkssäkerhetsgrupp som tillåter kluster-intern kommunikation.

5. Klicka på **skapa** att distribuera Azure Databricks-arbetsytan till det virtuella nätverket.

## <a name="advanced-resource-manager-configurations"></a>Avancerade resource manager-konfigurationer

Om du vill ha mer kontroll över konfigurationen för det virtuella nätverket – till exempel du vill använda befintliga undernät, använda befintliga nätverkssäkerhetsgrupper eller skapa egna säkerhetsregler – du kan använda följande Azure Resource Manager-mallar i stället för den portalen virtual network-konfiguration och arbetsytan distributionen.

### <a name="all-in-one"></a>Allt i ett

Du kan skapa ett virtuellt nätverk, nätverkssäkerhetsgrupper och Azure Databricks-arbetsytan allt i ett med den [allt-i-ett-mallen för Databricks VNet in arbetsytor](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

När du använder den här mallen behöver du inte göra några manuella vitlistning av undernätstrafik.

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Om du vill skapa nätverkssäkerhetsgrupper med de nödvändiga reglerna för ett befintligt virtuellt nätverk, använda den [Network Security Group mall för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

När du använder den här mallen behöver du inte göra några manuella vitlistning av undernätstrafik.

### <a name="virtual-network"></a>Virtuellt nätverk

Du kan skapa ett virtuellt nätverk med rätt offentliga och privata undernät med den [virtuella nätverk-mallen för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Om du använder den här mallen utan att också använda mallen network security grupper, måste du manuellt lägga till av regler för nätverkssäkerhetsgrupper som du använder med det virtuella nätverket.

### <a name="azure-databricks-workspace"></a>Azure Databricks-arbetsyta

Distribuera en Azure Databricks-arbetsyta till ett befintligt virtuellt nätverk som har offentliga och privata undernät och korrekt konfigurerad nätverkssäkerhetsgrupper som redan har konfigurerat genom att använda den [mallen för Databricks VNet-inmatning](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Om du använder den här mallen utan att också använda mallen network security grupper, måste du manuellt lägga till av regler för nätverkssäkerhetsgrupper som du använder med det virtuella nätverket.

## <a name="whitelisting-subnet-traffic"></a>Lista över tillåtna undernätstrafik

Om du inte använder den [Azure-portalen](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) eller [Azure Resource Manager-mallar](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) för att skapa säkerhetsgrupper för ditt nätverk, måste du manuellt godkänna följande trafik på dina undernät.

|Direction|Protokoll|Källa|Källport|Mål|Målport|
|---------|--------|------|-----------|-----------|----------------|
|Inkommande|\*|VirtualNetwork|\*|\*|\*|
|Inkommande|\*|Kontrollen plan NAT IP|\*|\*|22|
|Inkommande|\*|Kontrollen plan NAT IP|\*|\*|5557|
|Utgående|\*|\*|\*|WebApp IP|\*|
|Utgående|\*|\*|\*|SQL (tjänsttagg)|\*|
|Utgående|\*|\*|\*|Lagring (tjänsttagg)|\*|
|Utgående|\*|\*|\*|VirtualNetwork|\*|

Lista över tillåtna undernätstrafik med hjälp av följande IP-adresser. För SQL (metaarkiv) och Storage (lagring artefakt och loggfiler), bör du använda Sql och Storage [tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Azure Databricks-Region|Tjänst|Offentlig IP-adress|
|-----------------------|-------|---------|
|Östra USA|Kontrollplanet NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|USA, östra 2|Kontrollplanet NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Norra centrala USA|Kontrollplanet NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Centrala USA|Kontrollplanet NAT </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Södra centrala USA|Kontrollplanet NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Västra USA|Kontrollplanet NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Västra USA 2|Kontrollplanet NAT </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Centrala Kanada|Kontrollplanet NAT </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Östra Kanada|Kontrollplanet NAT </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Storbritannien, västra|Kontrollplanet NAT </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Storbritannien, södra|Kontrollplanet NAT </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Västra Europa|Kontrollplanet NAT </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Norra Europa|Kontrollplanet NAT </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Indien, centrala|Kontrollplanet NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Södra Indien|Kontrollplanet NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Indien, västra|Kontrollplanet NAT </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Sydostasien|Kontrollplanet NAT </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Östasien|Kontrollplanet NAT </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Östra Australien|Kontrollplanet NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Sydöstra Australien|Kontrollplanet NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, centrala|Kontrollplanet NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australien, centrala 2|Kontrollplanet NAT </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Östra Japan|Kontrollplanet NAT </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Västra Japan|Kontrollplanet NAT </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Felsökning

### <a name="workspace-launch-errors"></a>Arbetsytan starta fel

Starta en arbetsyta i ett anpassat virtuellt nätverk misslyckas på Azure Databricks inloggningsskärmen med följande fel: **”Vi har påträffade ett fel när din arbetsyta. Kontrollera att den anpassade nätverkskonfigurationen är korrekt och försök igen ”.**

Det här felet beror på en konfiguration för nätverk som inte uppfyller kraven. Bekräfta att du följt instruktionerna i det här avsnittet när du har skapat arbetsytan.

### <a name="cluster-creation-errors"></a>Fel vid skapande av kluster

**Instanser kan inte nås: Det gick inte att nå via SSH resurser.**

Möjlig orsak: trafik från kontrollplanet till arbetare är blockerad. Åtgärda genom att se till att inkommande säkerhetsregler uppfyller kraven. Om du distribuerar till ett befintligt virtuellt nätverk som är anslutna till ditt lokala nätverk, granskar du inställningarna med hjälp av informationen som anges i ansluter din Azure Databricks-arbetsyta till ditt lokala nätverk.

**Oväntat startfel: Ett oväntat fel uppstod när du konfigurerar klustret. Försök igen och kontakta Azure Databricks om problemet kvarstår. Internt felmeddelande: Tidsgränsen nåddes när placera noden.**

Möjlig orsak: trafik från arbetare till Azure Storage-slutpunkter är blockerad. Åtgärda genom att se till att utgående säkerhetsregler uppfyller kraven. Om du använder anpassade DNS-servrar kan du också kontrollera status för DNS-servrarna i det virtuella nätverket.

**Startfel för cloud-providern: En cloud provider-fel påträffades när du konfigurerar klustret. Se Azure Databricks-guiden för mer information. Azure felkod: AuthorizationFailed/InvalidResourceReference.**

Möjlig orsak: det virtuella nätverk eller undernät inte finns längre. Kontrollera att det virtuella nätverk och undernät finns.

**Klustret avslutas. Orsak: Spark startfel: Spark kunde inte starta i tid. Det här problemet kan orsakas av en felaktig Hive-metaarkiv, ogiltig Spark-konfigurationer eller felaktiga init-skript. Se loggar för Spark-drivrutinen att felsöka problemet och kontakta Databricks om problemet kvarstår. Internt felmeddelande: Det gick inte att starta Spark: Drivrutin gick inte att starta i tid.**

Möjlig orsak: Behållaren kan inte kommunicera med värdbaserade instans eller DBFS storage-konto. Åtgärda genom att lägga till en anpassad väg till undernät för lagringskontot med DBFS med nästa hopp som Internet.

### <a name="notebook-command-errors"></a>Anteckningsboken kommandot fel

**Kommandot svarar inte**

Möjlig orsak: worker-arbetare kommunikation blockeras. Åtgärda genom att kontrollera att de inkommande säkerhetsreglerna uppfyller kraven.

**Anteckningsboken arbetsflödet misslyckas med undantaget: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Möjlig orsak: trafik från arbetare till Azure Databricks Webapp blockeras. Åtgärda genom att kontrollera att de utgående säkerhetsreglerna uppfyller kraven.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera, transformera och läsa in data med Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
