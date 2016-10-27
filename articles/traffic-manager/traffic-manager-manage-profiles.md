<properties
    pageTitle="Hantera Azure Traffic Manager-profiler | Microsoft Azure"
    description="Den här artikeln beskriver hur du skapar, inaktiverar, aktiverar, tar bort och visar historiken för en Azure Traffic Manager-profil."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>


# <a name="manage-an-azure-traffic-manager-profile"></a>Hantera en Azure Traffic Manager-profil

Traffic Manager-profiler använder trafikroutningsmetoder för att kontrollera trafikdistributionen till dina molntjänster eller webbplats-slutpunkter. Den här artikeln förklarar hur du skapar och hanterar de här profilerna.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Skapa en Traffic Manager-profil med hjälp av Snabbregistrering

Du kan snabbt skapa en Traffic Manager-profil genom att använda Snabbregistrering på den klassiska Azure-portalen. Med Snabbregistrering kan du skapa profiler med grundläggande konfigurationsinställningar. Men du kan inte använda Snabbregistrering för konfigurationen av slutpunkter (molntjänster och webbplatser), redundansordningen för trafikroutningsmetoden för redundans eller övervakningsinställningar. När du har skapat din profil kan du konfigurera dessa inställningar på den klassiska Azure-portalen. Traffic Manager har stöd för upp till 200 slutpunkter per profil. De flesta användningsscenarier kräver dock endast ett litet antal slutpunkter.

### <a name="to-create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. **Distribuera dina molntjänster och webbplatser till din produktionsmiljö.** Mer information om molntjänster finns i [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Mer information om webbplatser finns i [Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Logga in på den klassiska Azure-portalen.** Klicka på **Ny** nere till vänster i portalen, klicka på **Nätverkstjänster > Traffic Manager** och klicka sedan på **Snabbregistrering** för att börja konfigurera din profil.
3. **Konfigurera DNS-prefix.** Ge din Traffic Manager-profil ett unikt DNS-prefixnamn. Du kan ange bara prefixet för ett Traffic Manager-domännamn.
4. **Välj prenumeration.** Välj lämplig Azure-prenumeration. Varje profil är associerad med en enda prenumeration. Om du bara har en prenumeration visas inte det här alternativet.
5. **Välj trafikroutningsmetod.** Välj trafikroutningsmetod i **trafikroutningsprincip**. Mer information om trafikroutningsmetoder finns i [Om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md).
6. **Klicka på ”Skapa” för att skapa profilen**. När profilkonfigurationen har slutförts hittar du din profil i Traffic Manager-fönstret på den klassiska Azure-portalen.
7. **Konfigurera slutpunkter, övervakning och ytterligare inställningar i den klassiska Azure-portalen.** Snabbregistrering konfigurerar bara grundläggande inställningar. Det är nödvändigt att konfigurera ytterligare inställningar som exempelvis listan över slutpunkter och redundansordning.


## <a name="disable,-enable,-or-delete-a-profile"></a>Inaktivera, aktivera eller ta bort en profil

Du kan inaktivera en befintlig profil så att Traffic Manager inte refererar användarbegäranden till de konfigurerade slutpunkterna. När du inaktiverar en Traffic Manager-profil, finns själva profilen och informationen den innehåller kvar och kan redigeras i Traffic Manager-gränssnittet.  Referenser återupptas när du återaktiverar profilen. När du skapar en Traffic Manager-profil i den klassiska Azure-portalen så aktiveras den automatiskt. Om en bestämmer att en profil inte längre behövs, kan du ta bort den.

### <a name="to-disable-a-profile"></a>Så här inaktiverar du en profil

1. Om du använder ett anpassat domännamn, ändra då CNAME-posten på din Internet-DNS-server så att den inte längre pekar mot din Traffic Manager-profil.
2. Trafiken slutar att dirigeras till slutpunkterna via Traffic Manager-profilinställningarna.
3. Välj den profil som du vill inaktivera. På Traffic Manager-sidan, markerar du profilen genom att klicka på kolumntexten bredvid profilnamnet. Observera att om du klickar på namnet i profilen eller pilen bredvid namnet, så öppnas inställningssidan för profilen.
4. När du har valt profilen klickar du på **Inaktivera** längst ned på sidan.

### <a name="to-enable-a-profile"></a>Så här aktiverar du en profil

1. Välj den profil som du vill inaktivera. På Traffic Manager-sidan, markerar du profilen genom att klicka på kolumntexten bredvid profilnamnet. Observera att om du klickar på namnet i profilen eller pilen bredvid namnet, så öppnas inställningssidan för profilen.
2. När du har valt profilen klickar du på **Aktivera** längst ned på sidan.
3. Om du använder ett anpassat domännamn, skapar du en CNAME-resurspost på din Internet-DNS-server som pekar mot domännamnet för din Traffic Manager-profil.
4. Trafiken börjar dirigeras till slutpunkterna igen.

### <a name="to-delete-a-profile"></a>Så här tar du bort en profil

1. Kontrollera att DNS-resursposten på Internet-DNS-servern inte längre använder en CNAME-resurspost som pekar på domännamnet för Traffic Manager-profilen.
2. Välj den profil som du vill inaktivera. På Traffic Manager-sidan, markerar du profilen genom att klicka på kolumntexten bredvid profilnamnet. Observera att om du klickar på namnet i profilen eller pilen bredvid namnet, så öppnas inställningssidan för profilen.
3. När du har valt profilen klickar du på **Ta bort** längst ned på sidan.

## <a name="view-traffic-manager-profile-change-history"></a>Visa Traffic Manager-profilens ändringshistorik

Du kan visa ändringshistoriken för din Traffic Manager-profil på den klassiska Azure-portalen i Hanteringstjänster.

### <a name="to-view-your-traffic-manager-change-history"></a>Så här visar du ändringshistoriken för Traffic Manager

1. Klicka på **Hanteringstjänster** i den vänstra rutan på den klassiska Azure-portalen.
2. Klicka på **Åtgärdsloggar** på sidan Hanteringstjänster.
3. På sidan Åtgärdsloggar kan du filtrera fram ändringshistoriken för Traffic Manager-profilen. När du har valt filtreringsalternativ klickar du på bockmarkeringen för att visa resultatet.

   - Om du vill visa ändringar för alla dina profiler, väljer du din prenumeration och ett tidsintervall och väljer sedan **Traffic Manager** från snabbmenyn **Typ**.
   - Om du vill filtrera efter profilnamn skriver du namnet på profilen i fältet **Tjänstnamn** eller väljer den från snabbmenyn.
   - Om du vill visa information för varje enskild ändring markerar du raden med den ändring som du vill visa och klickar sedan på **Information** längst ned på sidan. I fönstret **Åtgärdsinformation** kan du visa en XML-representation av API-objektet som skapades eller uppdaterades som en del av åtgärden.

## <a name="next-steps"></a>Nästa steg

- [Lägg till en slutpunkt](traffic-manager-endpoints.md)
- [Konfigurera routningsmetod vid redundans](traffic-manager-configure-failover-routing-method.md)
- [Konfigurera routningsmetod vid resursallokering](traffic-manager-configure-round-robin-routing-method.md)
- [Konfigurera routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)
- [Peka företagets Internetdomän till ett Traffic Manager-domännamn](traffic-manager-point-internet-domain.md)
- [Felsök degraderat tillstånd i Traffic Manager](traffic-manager-troubleshooting-degraded.md)


<!--HONumber=Oct16_HO3-->


