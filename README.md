# update_apps

Public Sub CheckForUpdates()
        Dim FilePath As String = Application.StartupPath & "\setting.ini"
        Dim ftpip = Readini(FilePath, "config ftp", "ip", "")
        Dim ftpuser = Readini(FilePath, "config ftp", "user", "")
        Dim ftppwd = Readini(FilePath, "config ftp", "pwd", "")
        Dim ftpfolder = Readini(FilePath, "config ftp", "svr", "")
        Dim ftpRequest As FtpWebRequest = DirectCast(WebRequest.Create("ftp://" & ftpip & "/"), FtpWebRequest)
        ftpRequest.Credentials = New NetworkCredential(ftpuser, ftppwd)
        ftpRequest.Method = WebRequestMethods.Ftp.ListDirectory
        Dim response As FtpWebResponse = DirectCast(ftpRequest.GetResponse(), FtpWebResponse)
        Dim streamReader As New StreamReader(response.GetResponseStream())
        Dim directories As New List(Of String)()
        Dim line As String = streamReader.ReadLine()
        ProgressBar2.Value = 25%
        While Not String.IsNullOrEmpty(line)
            directories.Add(line)
            line = streamReader.ReadLine()
        End While
        streamReader.Close()
        Using ftpClient As New WebClient()
            ProgressBar2.Value = 35%
            ftpClient.Credentials = New System.Net.NetworkCredential(ftpuser, ftppwd)
            ProgressBar2.Value = 55%
            For i As Integer = 0 To directories.Count - 1
                If directories(i).Contains(".") Then
                    Dim path As String = "ftp://" & ftpip & "/" + directories(i).ToString()
                    Dim trnsfrpth As String = "C:\" & ftpfolder & "\" + directories(i).ToString()
                    ftpClient.DownloadFile(path, trnsfrpth)
                End If
            Next
            ProgressBar2.Value = 75%
            ToolStripProgressBar1.Value = 20%
            btnupdate.Text = "Progress ..."
        End Using
        btnupdate.Text = "OK"
        rtbinfo.SelectionColor = Color.Green
        rtbinfo.SelectedText = "Succended......Please Wait Now " & vbCrLf & "apps'll " & vbCrLf & "Opening apps BOM ANGGUN" & vbCrLf & "after this please enable content   on MS access APP BOM "
        Shell("cmd.exe /c c:\" & ftpfolder & "\remote.cmd", AppWinStyle.Hide)
        ProgressBar2.Value = 100%
        ToolStripProgressBar1.Value = 100%
        CreateODBCToolStripMenuItem.Enabled = True
    End Sub
