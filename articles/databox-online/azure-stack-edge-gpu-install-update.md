---
title: Installera uppdatering på Azure Stack Edge GPU-enhet | Microsoft Docs
description: Beskriver hur du tillämpar uppdateringar med hjälp av Azure Portal och lokalt webb gränssnitt för Azure Stack Edge-GPU-enhet och Kubernetes-klustret på enheten
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 6bafeeeda686dafadc3f5104240f8d018de83aeb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268252"
---
# <a name="update-your-azure-stack-edge-with-gpu"></a>Uppdatera din Azure Stack Edge med GPU 

I den här artikeln beskrivs de steg som krävs för att installera uppdateringen på din Azure Stack Edge med GPU via det lokala webb gränssnittet och via Azure Portal. Du tillämpar program uppdateringar eller snabb korrigeringar för att hålla din Azure Stack Edge-enhet och tillhör ande Kubernetes-cluaster på enheten uppdaterad. 

> [!IMPORTANT]
> - Uppdatering **2008** motsvarar **2.1.1328.1904** program varu version på enheten. Information om den här uppdateringen finns i [viktig information](azure-stack-edge-gpu-2008-release-notes.md).
>
> - Kom ihåg att om du installerar en uppdatering eller snabb korrigering startas enheten om. Den här uppdateringen kräver att du installerar två uppdateringar i tur och ordning. Först installerar du enhetens program uppdateringar och sedan Kubernetes-uppdateringar. Med tanke på att Azure Stack Edge är en enda nod-enhet avbryts alla i/O-åtgärder och enheten upplever ett drift stopp på upp till 30 minuter för enhetens program uppdatering.

Om du vill installera uppdateringar på enheten måste du först konfigurera platsen för uppdaterings servern. När uppdaterings servern har kon figurer ATS kan du tillämpa uppdateringarna via Azure Portal gränssnittet eller det lokala webb gränssnittet.

Vart och ett av dessa steg beskrivs i följande avsnitt.

## <a name="configure-update-server"></a>Konfigurera uppdaterings Server

1. I det lokala webb gränssnittet går du till **konfigurations**  >  **uppdaterings Server**. 
   
    ![Konfigurera uppdateringar](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. I **Välj uppdaterings Server typ**, i list rutan, väljer du från Microsoft Update Server (standard) eller Windows Server Update Services.  
   
    Om du uppdaterar från Windows Server Update Services anger du Server-URI. Servern vid denna URI kommer att distribuera uppdateringarna på alla enheter som är anslutna till den här servern.

    ![Konfigurera uppdateringar](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS-servern används för att hantera och distribuera uppdateringar via en hanterings konsol. En WSUS-server kan också vara uppdaterings källa för andra WSUS-servrar inom organisationen. WSUS-servern som agerar som en uppdateringskälla kallas för en uppströms-server. I en WSUS-implementering måste minst en WSUS-server i nätverket kunna ansluta till Microsoft Update för att hämta tillgänglig uppdaterings information. Som administratör kan du bestämma beroende på nätverks säkerhet och-konfiguration – hur många andra WSUS-servrar som ansluter direkt till Microsoft Update.
    
    Mer information finns i [Windows Server Update Services (WSUS)](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Vi rekommenderar att du installerar uppdateringar via Azure Portal. Enheten söker automatiskt efter uppdateringar en gång om dagen. När uppdateringarna är tillgängliga visas ett meddelande i portalen. Sedan kan du hämta och installera uppdateringarna. 

> [!NOTE]
> Kontrol lera att enheten är felfri och att statusen visas som **online** innan du installerar uppdateringarna.

1. När uppdateringarna är tillgängliga för din enhet visas ett meddelande. Välj meddelandet eller från det övre kommando fältet, **Uppdatera enhet**. Detta gör att du kan tillämpa enhets program uppdateringar.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. På bladet **enhets uppdateringar** kontrollerar du att du har granskat de licens villkor som är associerade med nya funktioner i viktig information.

    Du kan välja att **Ladda ned och installera** uppdateringarna eller bara **Hämta** uppdateringarna. Du kan sedan välja att installera dessa uppdateringar senare.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    Om du vill hämta och installera uppdateringarna markerar du alternativet som uppdateras automatiskt när hämtningen är klar.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. Hämtningen av uppdateringar startar. Du ser ett meddelande om att nedladdningen pågår.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    En meddelande banderoll visas också i Azure Portal. Detta anger hämtnings förloppet. 

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Du kan välja det här meddelandet eller välja **Uppdatera enhet** för att visa detaljerad status för uppdateringen.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. När hämtningen är klar uppdateras meddelande banderollen för att visa att åtgärden har slutförts. Om du väljer att hämta och installera uppdateringarna startar installationen automatiskt.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Om du väljer att bara hämta uppdateringar väljer du meddelandet för att öppna bladet med **enhets uppdateringar** . Välj **Install** (Installera).
  
    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Du ser ett meddelande om att installationen pågår.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)

    Portalen visar också en informations avisering som indikerar att installationen pågår. Enheten är offline och är i underhålls läge.
    
    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Eftersom det här är en enhet med 1 nod startar enheten om när uppdateringarna har installerats. Den kritiska varningen under omstarten indikerar att enhetens pulsslag förloras.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Välj aviseringen för att se motsvarande enhets händelse.
    
    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Efter omstarten placeras enheten återigen i underhålls läge och en informations avisering visas för att indikera att.

    Om du väljer **Uppdatera enhet** i det övre kommando fältet kan du se förloppet för uppdateringarna.   

8. Enhets statusen uppdateras till **online** efter att uppdateringarna har installerats. 

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    Välj **enhets uppdateringar**från det övre kommando fältet. Kontrol lera att uppdateringen har installerats och att enhetens program varu version återspeglar detta.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

9. Du kommer återigen att se ett meddelande om att uppdateringar är tillgängliga. Detta är de Kubernetes uppdateringarna. Välj meddelandet eller Välj **Uppdatera enhet** i det övre kommando fältet.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Ladda ned Kubernetes-uppdateringarna. Du kan se att paket storleken skiljer sig jämfört med det tidigare uppdaterings paketet.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    Installations processen är identisk med enhets uppdateringarnas. Först laddas uppdateringarna ned.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. När uppdateringarna har hämtats kan du installera uppdateringarna. 

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    När uppdateringarna installeras försätts enheten i underhålls läge. Enheten startar inte om för Kubernetes-uppdateringarna. 

    När Kubernetes-uppdateringarna har installerats försvinner informations meddelandet eftersom inga ytterligare uppdateringar behövs. Enheten har nu den senaste versionen av enhets program och Kubernetes.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-20.png)


## <a name="use-the-local-web-ui"></a>Använd det lokala webb gränssnittet

Det finns två steg när du använder det lokala webb gränssnittet:

* Hämta uppdateringen eller snabb korrigeringen
* Installera uppdateringen eller snabb korrigeringen

Vart och ett av dessa steg beskrivs ingående i följande avsnitt.


### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

Utför följande steg för att ladda ned uppdateringen. Du kan hämta uppdateringen från den Microsoft-angivna platsen eller från Microsoft Update katalogen.


Utför följande steg för att ladda ned uppdateringen från Microsoft Update katalogen.

1. Starta webbläsaren och gå till [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Sökkatalog](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. I rutan Sök i Microsoft Updates katalogen anger du Knowledge Base-numret för snabb korrigeringen eller villkoren för den uppdatering som du vill ladda ned. Ange till exempel **Azure Stack Edge**och klicka sedan på **Sök**.
   
    Uppdaterings listan visas som **Azure Stack Edge 2006**.
   
    ![Sökkatalog](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)

4. Välj **Download** (Ladda ned). Det finns två filer att ladda ned med *SoftwareUpdatePackage.exe* och *Kubernetes_Package.exe* suffix som motsvarar enhets program uppdateringar respektive Kubernetes uppdateringar. Ladda ned filerna till en mapp på det lokala systemet. Du kan också kopiera mappen till en nätverks resurs som kan kontaktas från enheten.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

Innan du installerar uppdateringen eller hotfixen bör du kontrol lera att:

 - Du har uppdateringen eller snabb korrigeringen som hämtats antingen lokalt på värden eller kan nås via en nätverks resurs.
 - Enhetens status är felfri så som visas på sidan **Översikt** i det lokala webb gränssnittet.

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

Den här proceduren tar cirka 20 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

1. I det lokala webb gränssnittet går du till **Underhåll**  >  **program uppdatering**. Anteckna den program varu version som du kör. 
   
   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Ange sökvägen till uppdaterings filen. Du kan också bläddra till installations filen för uppdateringen om den placeras på en nätverks resurs. Välj program uppdaterings filen med *SoftwareUpdatePackage.exe* suffix.

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. Välj **Använd**. 

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. När du uppmanas att bekräfta, väljer du **Ja** för att fortsätta. Om enheten är en enskild nod enhet startas enheten om när uppdateringen har installerats och det finns avbrott. 
   
   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användar gränssnittet är inte tillgängligt under denna varaktighet.
   
6. När omstarten är klar tas du till **inloggnings** sidan. Kontrol lera att enhetens program vara har uppdaterats genom att gå till **Underhåll**  >  **program uppdatering**i det lokala webb gränssnittet. Den program varu version som visas i det här exemplet är **2.0.1257.1591**.

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png) 

7. Du kommer nu att uppdatera Kubernetes-program versionen. Upprepa stegen ovan. Ange en sökväg till Kubernetes-uppdaterings filen med *Kubernetes_Package.exe* suffixet.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. Välj **Använd**. 

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. När du uppmanas att bekräfta, väljer du **Ja** för att fortsätta. 

10. När Kubernetes-uppdateringen har installerats sker ingen ändring av den program vara som visas i **underhålls**  >  **program uppdateringen**. 


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar din Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).
