
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Visa VMs schemalagt underhåll i portalen

När ett planerat underhåll wave schemaläggs och meddelanden skickas, kan du se listan över virtuella datorer som påverkas av kommande Underhåll wave. 

Du kan använda Azure portal och leta efter virtuella datorer som är schemalagda för underhåll.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I det vänstra navigeringsfönstret klickar du på **virtuella datorer**.

3. Klicka på i fönstret virtuella datorer i **kolumner** knappen för att öppna listan över tillgängliga kolumner.

4. Välj och Lägg till följande kolumner:

   **Underhåll** -visar Underhåll status för den virtuella datorn. Följande är möjliga värden:
      
      | Värde | Beskrivning |
      |-------|-------------|
      | Börja nu | Den virtuella datorn är i underhållsfönstret självbetjäning som gör att du kan initiera underhållet själv. Se nedan om hur du startar Underhåll på den virtuella datorn | 
      | Schemalagd | Den virtuella datorn är schemalagt för underhåll utan något alternativ att initiera underhåll. Du kan lära dig för underhåll genom att välja fönstret automatiskt schemalagda i den här vyn eller genom att klicka på den virtuella datorn | 
      | Slutförd | Du har initierat och slutföra Underhåll på den virtuella datorn. | 
      | Överhoppad| Du har valt för att initiera underhåll utan framgång. Du kommer inte att använda alternativet självbetjäning underhåll. Den virtuella datorn måste startas av Azure under fasen schemalagt underhåll. | 

   **Underhåll proaktiva** -visar tidsfönstret vid automatisk start Underhåll på dina virtuella datorer.
   
   **Underhåll schemalagda** -visar tidsfönstret när Azure startar om den virtuella datorn för att kunna utföra underhåll. 




## <a name="notification-and-alerts-in-the-portal"></a>Meddelanden och aviseringar i portalen

Azure kommunicerar ett schema för planerat underhåll genom att skicka ett e-postmeddelande till prenumerationen ägare och Medägare gruppen. Du kan lägga till ytterligare mottagare och kanaler denna kommunikation genom att skapa Azure aktivitet loggen aviseringar. Mer information finns i [övervakaren prenumeration aktivitet med Azure-aktivitetsloggen] (... / articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj på menyn till vänster **övervakaren**. 
3. I den **Monitor - aktivitetsloggen** väljer **aviseringar**.
4. I den **Monitor - aviseringar** rutan klickar du på **+ Lägg till aktivitet loggen avisering**.
5. Fyll i den **Lägg till aktivitet loggen avisering** sidan och se till att ange följande **kriterier**: **typen**: Underhåll **Status**: Alla (inte anger status till aktiv eller löst) **nivå**: alla
    
Mer information om hur du konfigurerar aktivitet loggen aviseringar finns [och skapa aktiviteten Logga varningar](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Starta Underhåll på den virtuella datorn från portalen

Du kommer att kunna se mer underhållsrelaterade information när du tittar på VM-information.  
Längst upp i Detaljvyn VM läggs en ny avisering menyfliksområdet om den virtuella datorn ingår i ett planerat underhåll wave. Dessutom läggs ett nytt alternativ för att starta Underhåll när det är möjligt. 


Klicka på Underhåll meddelandet om du vill se underhållssidan med mer information på planerat underhåll. Därifrån kommer du att kunna **starta Underhåll** på den virtuella datorn.

När du startar underhåll, den virtuella datorn kommer att startas om och underhåll status kommer att uppdateras för att återspegla resultatet inom några minuter.

Om du har missat fönstret där du kan starta Underhåll att du fortfarande kunna se fönstret när den virtuella datorn kommer att startas om av Azure. 
