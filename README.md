# coresponding-
       EndIf             Case 2                 $query_server = "4.2.2.1"             Case 3                 $query_server = "67.138.54.100"             Case 4                 $query_server = "208.67.222.222"             Case 5                 $query_server = "4.2.2.2"             Case 6                 $query_server = "4.2.2.3"             Case 7                 $query_server = "208.67.220.220"             Case 8                 $query_server = "4.2.2.4"             Case 9                 $query_server = "4.2.2.5"             Case 10                 $query_server = "4.2.2.6"             Case 11                 Return -1         EndSwitch                  If $query_server &lt;> "" Then             UDPStartup()              Local $sock             $sock = UDPOpen($query_server, 53)             If $sock = -1 Then ; ok, that happens                 UDPCloseSocket($sock)                 UDPShutdown()                 ContinueLoop ; change server and try again             EndIf                          UDPSend($sock, $server_bin) ; sending query              Local $tik = 0             Do                 $data = UDPRecv($sock, 512)                 $tik += 1                 Sleep(100)             Until $data &lt;> "" Or $tik = 8 ; waiting reasonable time for the response              UDPShutdown() ; stopping service                          If $data &lt;> "" And StringRight(BinaryMid($data, 2, 1), 2) = $identifier Then                 Return $data ; if there is data for us, return             EndIf         EndIf     Next          Return -1      EndFunc   ;==>MXQueryServer   Func ExtractMXServerData($binary_data)      Local $num_answ = Dec(StringMid($binary_data, 15, 4)) ; representing number of answers provided by the server          Local $arr = StringSplit($binary_data, "C00C000F0001", 1) ; splitting input; "C00C000F0001" - translated to human: "this is the answer for your MX query"      If $num_answ &lt;> $arr[0] - 1 Or $num_answ = 0 Then Return -1 ; dealing with possible options          Local $pref[$arr[0]] ; preference number(s)     Local $server[$arr[0]] ; server name(s)     Local $output[2][$arr[0]] ; this goes out containing both server names and coresponding preference numbers     $output[0][0] = $arr[0] - 1      Local $offset = 10 ; initial offset
