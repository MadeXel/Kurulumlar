<p style="font-size:14px" align="right">
 <a href="https://t.me/nodeistt" target="_blank"><img src="https://github.com/Ahbaay34/Testnet_Kurulumlar/blob/fee87fe32609c1704206721b9fb16e4c5de75a96/telegramlogo.png" width="30"/></a><br>Telegrama Katıl<br>
<a href="https://nodeist.com/" target="_blank"><img src="https://github.com/Ahbaay34/Testnet_Kurulumlar/blob/a1520438004a799bba57311cd0cfc1f9a047691e/logo.png" width="60"/></a><br> Websitemizi Ziyaret Et 
</p>

<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/165055511-83e8a2d3-1700-4d26-af27-abcc825573a7.png">
</p>

# Defund Node Kurulumu — defund-private-1

Resmi Dökümanlar:
>- [Validator Kurulumu](https://github.com/defund-labs/defund/blob/main/testnet/private/validators.md)
>- [DeFund Tokenomics](https://medium.com/defund-finance/detf-token-economics-release-schedule-78a8d32713a5)
>- [FundDrop Breakdown](https://medium.com/defund-finance/airdrop-d-c2685d282858)

Gezgin:
>-  https://defund.explorers.guru/

## Kullanışlı Araçlar ve Referanslar
> Doğrulayıcı düğümünüz için monitör kullanmak ve alarm oluşturmak için [buraya tıklayın](https://github.com/kj89/testnet_manuals/blob/main/defund/monitoring/README.md)
>
> Doğrulayıcı düğümünüzü başka bir sunucuya taşımak için [buraya tıklayın](https://github.com/kj89/testnet_manuals/blob/main/defund/migrate_validator.md)

## Defund Full Node Kurulum Adımları
### Seçenek 1 (Otomatik Kurulum)
Aşağıdaki otomatik komut dosyasını kullanarak defund fullnode'unuzu birkaç dakika içinde kurabilirsiniz. Doğrulayıcı düğüm adınızı(NODE NAME) girmenizi isteyecektir!


```
wget -O defund.sh https://raw.githubusercontent.com/kj89/testnet_manuals/main/defund/defund.sh && chmod +x defund.sh && ./defund.sh
```

### Option 2 (manual)
Düğümü manuel olarak kurmayı tercih ederseniz, [manuel kurulum kılavuzu](https://github.com/kj89/testnet_manuals/blob/main/defund/manual_install.md)'na göz atabilirsiniz.

### Kurulum Sonrası Adımlar
Kurulum bittiğinde lütfen değişkenleri sisteme yükleyin:
```
source $HOME/.bash_profile
```

Ardından, doğrulayıcınızın blokları senkronize ettiğinden emin olmalısınız. Senkronizasyon durumunu kontrol etmek için aşağıdaki komutu kullanabilirsiniz.
```
defundd status 2>&1 | jq .SyncInfo
```

### Cüzdan Oluşturma
Yeni cüzdan oluşturmak için aşağıdaki komutu kullanabilirsiniz. Hatırlatıcıyı(mnemonic) kaydetmeyi unutmayın.
```
defundd keys add $WALLET
```

(İSTEĞE BAĞLI) Cüzdanınızı hatırlatıcı(mnemonic) kullanarak kurtarmak için:
```
defundd keys add $WALLET --recover
```

Mevcut cüzdan listesini almak için:
```
defundd keys list
```

### Cüzdan Bilgilerini Kaydet
Cüzdan Adresi Ekleyin:
```
WALLET_ADDRESS=$(defundd keys show $WALLET -a)
```

Valoper Adresi Ekleyin:
```
VALOPER_ADDRESS=$(defundd keys show $WALLET --bech val -a)
```

Değişkenleri sisteme yükleyin:
```
echo 'export WALLET_ADDRESS='${WALLET_ADDRESS} >> $HOME/.bash_profile

echo 'export VALOPER_ADDRESS='${VALOPER_ADDRESS} >> $HOME/.bash_profile

source $HOME/.bash_profile
```

### Musluğu kullanarak cüzdan bakiyenizi arttırın

defund-private-1 test ağında 20 ücretsiz jeton almak için:
* https://bitszn.com/faucets.html sitesine gidin.
* `COSMOS` sekmesine geçin ve `DeFund.Finance Testnet` seçin.
* cüzdan adresinizi girin ve `Request` butonuna tıklayın.

### Doğrulayıcı oluştur
Doğrulayıcı oluşturmadan önce lütfen en az 1 fetf'ye sahip olduğunuzdan (1 fetf 1000000 ufetf'e eşittir) ve düğümünüzün senkronize olduğundan emin olun.

Cüzdan bakiyenizi kontrol etmek için:
```
defundd query bank balances $WALLET_ADDRESS
```
> Cüzdanınızda bakiyenizi göremiyorsanız, muhtemelen düğümünüz hala eşitleniyordur. Lütfen senkronizasyonun bitmesini bekleyin ve ardından devam edin. 

Doğrulayıcıyı çalıştırma komutunu yazalım:
```
defundd tx staking create-validator \
  --amount 1000000ufetf \
  --from $WALLET \
  --commission-max-change-rate "0.01" \
  --commission-max-rate "0.2" \
  --commission-rate "0.07" \
  --min-self-delegation "1" \
  --pubkey  $(defundd tendermint show-validator) \
  --moniker $NODENAME \
  --chain-id $CHAIN_ID
```

## Güvenlik
Anahtarlarınızı korumak için lütfen temel güvenlik kurallarına uyduğunuzdan emin olun.

### Kimlik doğrulama için ssh anahtarlarını ayarlayın
Sunucunuza kimlik doğrulaması için ssh anahtarlarının nasıl kurulacağına dair iyi bir eğitim [burada bulunabilir](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)

### Temel Güvenlik Duvarı güvenliği
ufw'nin durumunu kontrol ederek başlayın.
```
sudo ufw status
```

Varsayılanı, giden bağlantılara izin verecek, ssh ve 26656 hariç tüm gelenleri reddedecek şekilde ayarlayın. SSH oturum açma girişimlerini sınırlayın.
```
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow ssh/tcp
sudo ufw limit ssh/tcp
sudo ufw allow 26656,26660/tcp
sudo ufw enable
```

## Monitör
Doğrulayıcı sağlık durumunuzu izlemek ve bu konuda alarm kurmak istiyorsanız [buraya tıklayarak ilgili dökümana göz atın.](https://github.com/kj89/testnet_manuals/blob/main/defund/monitoring/README.md)

## Senkronizasyon süresini hesaplayın

Bu komut dosyası, düğümünüzü tam olarak senkronize etmenin ne kadar zaman alacağını tahmin etmenize yardımcı olacaktır. 
5 dakikalık bir süre boyunca senkronize edilen dakika başına ortalama blokları ölçer ve ardından size sonuçlar verir.
```
wget -O synctime.py https://raw.githubusercontent.com/kj89/testnet_manuals/main/defund/tools/synctime.py && python3 ./synctime.py
```

## Şu anda bağlı olan eşler listesini kimlikleri ile alın
```
curl -sS http://localhost:26657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'
```

## Kullanışlı Komutlar
### Servis Yönetimi
Logları Kontrol Et:
```
journalctl -fu defundd -o cat
```

Servisi Başlat:
```
systemctl start defundd
```

Servisi Durdur:
```
systemctl stop defundd
```

Servisi Yeniden Başlat:
```
systemctl restart defundd
```

### Node Bilgileri
Senkronizasyon Bilgisi:
```
defundd status 2>&1 | jq .SyncInfo
```

Validator Bilgisi:
```
defundd status 2>&1 | jq .ValidatorInfo
```

Node Bilgisi:
```
defundd status 2>&1 | jq .NodeInfo
```

Node ID Göser:
```
defundd tendermint show-node-id
```

### Cüzdan İşlemleri
Cüzdanları Listele:
```
defundd keys list
```

Mnemonic kullanarak cüzdanı kurtar:
```
defundd keys add $WALLET --recover
```

Cüzdan Silme:
```
defundd keys delete $WALLET
```

Cüzdan Bakiyesi Sorgulama:
```
defundd query bank balances $WALLET_ADDRESS
```

Cüzdandan Cüzdana Bakiye Transferi:
```
defundd tx bank send $WALLET_ADDRESS <TO_WALLET_ADDRESS> 10000000ufetf
```

### Oylama
```
defundd tx gov vote 1 yes --from $WALLET --chain-id=$CHAIN_ID
```

### Stake, Delegasyon ve Ödüller
Delegate İşlemi:
```
defundd tx staking delegate $VALOPER_ADDRESS 10000000ufetf --from=$WALLET --chain-id=$CHAIN_ID --gas=auto
```

Payını doğrulayıcıdan başka bir doğrulayıcıya yeniden devretme:
```
defundd tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000ufetf --from=$WALLET --chain-id=$CHAIN_ID --gas=auto
```

Tüm ödülleri çek:
```
defundd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$CHAIN_ID --gas=auto
```

Komisyon ile ödülleri geri çekin:
```
defundd tx distribution withdraw-rewards $VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$CHAIN_ID
```

### Doğrulayıcı Yönetimi
Validatörü Düzenle:
```
defundd tx staking edit-validator \
--moniker=$NODENAME \
--identity=1C5ACD2EEF363C3A \
--website="http://nodeist.com" \
--details="Providing professional staking services with high performance and availability. Find me at Discord: kjnodes#8455 and Telegram: @kjnodes" \
--chain-id=$CHAIN_ID \
--from=$WALLET
```

Hapisten Kurtul(Unjail): 
```
defundd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$CHAIN_ID \
  --gas=auto
```