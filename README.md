# PowerShell RDP session log script
Bu PowerShell scripti, Windows işletim sistemi üzerinde çalışan bir bilgisayarda son 7 gün (gün sayısı düzenlenebilir) içinde gerçekleşen uzak masaüstü (Remote Desktop Protocol, RDP) bağlantı olaylarını (logon ve logoff) izlemek için kullanılır. İşte bu script ile ilgili ayrıntılı bir açıklama ve kullanım senaryoları:

```
$logs = get-eventlog system -ComputerName powershell-ozan -source Microsoft-Windows-Winlogon -After (Get-Date).AddDays(-7);
```

Bu komut, belirtilen bilgisayar (powershell-ozan bu bölümü kendi bilgisayar adınız ile güncelleyeiniz) üzerindeki 'System' event logundan, 'Microsoft-Windows-Winlogon' kaynağına ait, son 7 gün içindeki olayları çeker. Bu, kullanıcıların sisteme giriş yapma ve çıkma (logon ve logoff) olaylarını içerir.

```$res = @();
ForEach ($log in $logs) {
    if($log.instanceid -eq 7001) {$type = "Logon"}
    ElseIf ($log.instanceid -eq 7002){$type="Logoff"}
    Else {Continue}
    $res += New-Object PSObject -Property @{
        Time = $log.TimeWritten;
        "Event" = $type;
        User = (New-Object System.Security.Principal.SecurityIdentifier $Log.ReplacementStrings[1]).Translate([System.Security.Principal.NTAccount])
    };
}
$res
```

Ne Zaman Kullanılır?

Bu script, ağ güvenliği ve kullanıcı etkinliklerini izleme açısından çok değerlidir. Özellikle aşağıdaki durumlarda kullanılabilir:

  Güvenlik Denetimi: Yetkisiz veya şüpheli RDP oturum aktivitelerini tespit etmek.
  Kullanıcı Etkinlik Takibi: Çalışanların veya kullanıcıların ne zaman ve ne sıklıkta uzaktan bağlantı kurduğunu izlemek.
  Uyumluluk Raporlama: Çeşitli düzenleyici gerekliliklere uyum sağlamak için kullanıcı giriş/çıkış kayıtlarını saklamak.

Neden Kullanılır?

  Güvenlik Açıklarını Azaltmak: Uzaktan erişim aktivitelerini izleyerek, olası güvenlik açıklarını önceden tespit edebilir ve müdahale edebilirsiniz.
  Detaylı Kullanıcı Logları: Her kullanıcı için detaylı giriş ve çıkış zamanlarını görebilir, bu bilgileri güvenlik veya performans değerlendirmeleri için kullanabilirsiniz.
  Otomasyon ve Kolaylık: Bu script, manuel log kontrolü yapma zorunluluğunu ortadan kaldırır ve büyük log verileri üzerinde hızlı ve etkin bir şekilde çalışmanızı sağlar.

Bu PowerShell scripti, IT yöneticileri, sistem yöneticileri ve güvenlik analistleri tarafından sıklıkla kullanılır ve organizasyonların IT altyapısının daha güvenli ve verimli bir şekilde yönetilmesine olanak tanır.
