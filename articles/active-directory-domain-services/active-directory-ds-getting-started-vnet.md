<properties
    pageTitle="Azure AD Domain Services: Skapa eller välja ett virtuellt nätverk | Microsoft Azure"
    description="Komma igång med Azure Active Directory Domain Services (förhandsversion)"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="maheshu"/>

# Azure AD Domain Services *(förhandsversion)* – Skapa eller välja ett virtuellt nätverk

## Riktlinjer för att välja ett virtuellt Azure-nätverk
Ha följande riktlinjer i åtanke när du väljer ett virtuellt nätverk som ska användas med Azure AD Domain Services:

- Var noga med att välja ett virtuellt nätverk i en region som stöds av Azure AD Domain Services. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

- Om du planerar att använda ett befintligt virtuellt nätverk ser du till att det är ett regionalt virtuellt nätverk. Virtuella nätverk som använder mekanismen för äldre tillhörighetsgrupper kan inte användas med Azure AD Domain Services. Du måste [migrera äldre virtuella nätverk till regionala virtuella nätverk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

- Om du planerar att använda ett befintligt virtuellt nätverk kontrollerar du att det inte finns några anpassade DNS-servrar konfigurerade för det virtuella nätverket. Azure AD Domain Services stöder inte DNS-servrar som är anpassade/BYO (Bring Your Own).

- Om du planerar att använda ett befintligt virtuellt nätverk kontrollerar du att det inte finns någon befintlig domän med samma domännamn i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Anta vidare att du försöker aktivera en Azure AD Domain Services-hanterad domän med samma domännamn (t.ex. contoso.com) i det virtuella nätverket. I det här scenariot returneras ett fel när du försöker aktivera Azure AD Domain Services. Det beror på namnkonflikter för domännamnet i det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

- Välj det virtuella nätverk som för närvarande är eller ska vara värd för virtuella datorer som behöver åtkomst till Azure AD Domain Services. Du kan inte flytta Domain Services till ett annat virtuellt nätverk när du har aktiverat tjänsten.

- Azure AD Domain Services stöds inte med virtuella nätverk som skapats med Azure Resource Manager. Du kan [ansluta ett klassiskt virtuellt nätverk till ett ARM-baserat virtuellt nätverk](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) för att använda Azure AD Domain Services i ett virtuellt nätverk som har skapats med Azure Resource Manager.


## Uppgift 2: Skapa ett virtuellt Azure-nätverk
Nästa konfigurationsåtgärd är att skapa ett virtuellt Azure-nätverk som du vill aktivera Azure AD Domain Services i. Du kan hoppa över det här steget om du redan har ett befintligt virtuellt nätverk som du vill använda.

> [AZURE.NOTE] Se till att det virtuella Azure-nätverk som du skapar eller väljer att använda med Azure AD Domain Services tillhör en Azure-region som stöds av Azure AD Domain Services. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

Skriv ned namnet på det virtuella nätverket så att du väljer rätt virtuellt nätverk när du aktiverar Azure AD Domain Services i ett efterföljande konfigurationssteg.

Utför följande konfigurationssteg för att skapa ett virtuellt Azure-nätverk som du vill aktivera Azure AD Domain Services i.

1. Gå till **den klassiska Azure-portalen** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Välj noden **Nätverk** i det vänstra fönstret.

3. Klicka på **Nytt** i åtgärdsfönstret längst ned på sidan.

    ![Nod för virtuella nätverk](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Välj **Virtuellt nätverk** i noden **Nätverkstjänster**.

5. Skapa ett virtuellt nätverk genom att klicka på **Snabbregistrering**.

    ![Virtuellt nätverk – snabbregistrering](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Ange ett **namn** för det virtuella nätverket. Du kan också välja att konfigurera **adressutrymmet** eller **maximalt antal virtuella datorer** för det här nätverket. Du kan lämna inställningen Inget för DNS-servern tills vidare. Den här inställningen uppdateras när du har aktiverat Azure AD Domain Services.

7. Var noga med att välja en Azure-region som stöds i listrutan **Plats**. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt. Det här är ett viktigt steg. Om du väljer ett virtuellt nätverk i en Azure-region som inte stöds av Azure AD Domain Services kan du inte aktivera tjänsten i det virtuella nätverket.

8. Skapa det virtuella nätverket genom att klicka på **Skapa ett virtuellt nätverk**.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## Uppgift 3: Aktivera Azure AD Domain Services
Nästa konfigurationsåtgärd är att [aktivera Azure AD Domain Services](active-directory-ds-getting-started-enableaadds.md).



<!--HONumber=sep16_HO1-->


