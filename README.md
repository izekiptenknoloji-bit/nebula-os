# Nebula OS

Sıfırdan yazılmış x86_64 işletim sistemi çekirdeği. Linux'tan, BSD'den ya da
başka bir çekirdekten türetilmedi — GDT'den sayfalama tablolarına, disk
sürücüsünden ağ yığınına kadar her satırı bu proje için yazıldı.

Bu depo **önyüklenebilir ISO imajını** dağıtır. İndirmek için
[Releases](../../releases) bölümüne bakın.

---

## Güvenlik: canlı kip

**ISO diske hiçbir şey yazmaz.** Yazma yolları çekirdek seviyesinde kapalı;
disk sürücüleri (ATA ve NVMe) her yazma isteğini açılış kipini kontrol ederek
reddeder:

```
[ ENGEL ] ata: canli kipte yazma reddedildi (LBA 2048)
```

Kurulum yalnızca çekirdek komut satırında `install` sözcüğü geçtiğinde açılır;
dağıtılan ISO'da o giriş yok. Yani kendi bilgisayarınızda çalıştırmak
diskinizdeki verilere dokunmaz.

## Nasıl açılır

**USB bellek** — ISO hibrit (isohybrid), yani doğrudan yazılabilir:

```bash
sudo dd if=nebula-os.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

`/dev/sdX` yerine kendi USB aygıtınızı yazın. Yanlış aygıt seçmek o diski
siler — `lsblk` ile iki kez doğrulayın.

Windows'ta [Rufus](https://rufus.ie) ile "DD image" kipinde yazabilirsiniz.
Ventoy kullanıyorsanız ISO'yu doğrudan Ventoy bölümüne kopyalamanız yeterli;
USB'deki diğer dosyalarınız durur.

**QEMU** — donanıma dokunmadan denemek için:

```bash
qemu-system-x86_64 -M pc -m 512M -cdrom nebula-os.iso -boot d
```

BIOS ve UEFI, ikisi de destekleniyor.

## Ne var içinde

| Katman | Durum |
|---|---|
| Önyükleme | Limine protokolü, BIOS + UEFI, menüsüz doğrudan açılış |
| Bellek | Fiziksel bitmap ayırıcı, 4 seviyeli sayfalama, çekirdek yığını |
| Kesmeler | GDT, IDT, 8259A PIC, 8254 zamanlayıcı |
| Ekran | Framebuffer, Inter yazı tipi (tam Türkçe karakter desteği), masaüstü |
| Giriş | PS/2 klavye, kaydırılabilir konsol |
| Depolama | ATA PIO, ATAPI (CD), NVMe, PCI numaralandırma |
| Dosya sistemleri | ISO9660, FAT32 (uzun dosya adı okuma **ve** yazma) |
| Grafik | virtio-gpu 2D |
| Ağ | Ethernet, ARP, IPv4, ICMP — 43 sınamalık öz sınama koşumuyla |

Ağ yığını, kart bulunamadığında bile kendini sınar: sahte bir ağ aygıtına
çerçeve enjekte edip ürettiği cevabı bayt bayt doğrular.

```
[  OK  ] nettest: 43 sinamanin tamami gecti
```

## Sınanan yapılandırmalar

- QEMU 8.x, `-M pc`, BIOS ve UEFI (OVMF)
- Gerçek donanım: UEFI üzerinden USB açılışı

Açılmayan bir makineniz olursa ekran görüntüsüyle birlikte
[issue](../../issues) açın — özellikle ekran çözünürlüğü ve donanım modeli
işe yarar.

## Kaynak kod

Kaynak henüz bu depoda değil. Şu an ~11.000 satır C++ ve assembly.

## Lisans

Henüz belirlenmedi. ISO içindeki Limine önyükleyicisi BSD-2-Clause
lisanslıdır ve telif hakkı sahiplerine aittir.
