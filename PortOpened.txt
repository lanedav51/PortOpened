$TxtPath = "InsertFileHere"
$File = Import-Csv $TxtPath
$Computers = $File.$Computers
$Ports = $File.Ports

foreach($Computer in $Computers)
{
    Enter-PSSession -ComputerName $Computer
    foreach($Port in $Ports)
    {
        $obj = new-object psobject -Property @{
            Port = $Port
            ComputerName = $Computer
        }
        $TCPTest = (Test-NetConnection -Port $Port -ComputerName $Computer).TcpTestSucceeded
        $i = 0
        if($TCPTest -eq "True" -AND $i -eq 0)
        {
            $obj | Export-Csv -Path PortOpened.csv
            $i++
        }
        elseif($TCPTest -eq "True" -AND $i -ge 0)
        {
            $obj | Export-Csv -Path PortOpened.csv -Append
            $i++
        }
        else
        {
            Write-Host "$Computer has port $port closed."
        }
    }
    Exit-PSSession
}