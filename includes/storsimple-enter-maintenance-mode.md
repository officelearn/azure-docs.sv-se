<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Ange underhållsläge
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**.
2. Ange lösenordet. Standardlösenordet är **Password1**.
3. I Kommandotolken, skriver du:
   
     `Enter-HcsMaintenanceMode`
4. Visas ett varningsmeddelande som talar om att underhållsläge ska störa alla i/o-begäranden och sever anslutningen till den klassiska Azure portalen och du uppmanas att bekräfta. Typen **Y** anger underhållsläge.
   
    Både domänkontrollanter startas om. När omstarten är klar, visas ett annat meddelande som anger att enheten är i underhållsläge.

