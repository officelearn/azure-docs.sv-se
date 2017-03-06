När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. När du kopplar bort en disk tar du bort den från den virtuella datorn, men du tar inte bort den från Azure-lagringskontot.

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.  

> [!NOTE]
> För att koppla bort en operativsystemdisk måste du först ta bort den virtuella datorn.
>

## <a name="find-the-disk"></a>Hitta disken
Följ de här stegen om du inte känner till namnet på disken eller vill kontrollera det innan du kopplar från den.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på **Virtual Machines** och välj sedan den lämpliga virtuella enheten.

3. Klicka på **Diskar** under **Inställningar** längs vänsterkanten på instrumentpanelen för den virtuella datorn.

 Instrumentpanelen för den virtuella datorn visar namn och typ för alla anslutna diskar. Den här skärmen visar till exempel en virtuell dator med en operativsystemdisk och en datadisk:

    ![Hitta datadisk](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Koppla från disken
1. I Azure Portal klickar du på **Virtuella datorer** och sedan på namnet på den virtuella dator som har datadisken som du vill koppla från.

2. Klicka på **Diskar** under **Inställningar** längs vänsterkanten på instrumentpanelen för den virtuella datorn.

3. Klicka på den disk som du vill koppla från.

  ![Identifiera disken du vill koppla från](./media/howto-detach-disk-windows-linux/disklist.png)

4. Klicka på **Koppla från** i kommandofältet.

  ![Leta upp kommandot för att koppla från](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Klicka på **Ja** i bekräftelsefönstret för att koppla från disken.

  ![Bekräfta frånkoppling av disken](./media/howto-detach-disk-windows-linux/confirmdetach.png)

Disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.
