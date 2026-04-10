# Arc-Testnet-Deploy
Arc Testnet Akıllı Kontrat Deploy Etme 

# 🛠️ Adım 0 — Sistemi Hazırlayın
Önce sistemi güncelleyelim ve gerekli araçları kuralım:
```
apt update && apt upgrade -y
apt install curl git build-essential -y
```
# ⚙️ Adım 1 — Foundry Kurulumu
```
curl -L foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```
# Kurulum bitince kontrol edin:
```
forge --version
```
Versiyon numarası görüyorsanız kurulum başarılı.

# 📁 Adım 2 — Projeyi Başlatın
```
forge init arc-greeter && cd arc-greeter
```
# 🔗 Adım 3 — RPC Adresini Kaydedin
```
touch .env
nano .env
```
# Açılan editöre yapıştırın:
```
ARC_TESTNET_RPC_URL="https://rpc.testnet.arc.network"
```
Kaydedin: Ctrl+X → Y → Enter

# 👛 Adım 4 — Cüzdan Oluşturun
```
cast wallet new
```
# Çıktıda şunu göreceksiniz:
```
Address:     0xABC123...
Private key: 0xDEF456...
```
⚠️ Bu bilgileri güvenli bir yere kopyalayın. Private key'i kimseyle paylaşmayın.
# Şimdi private key'i .env dosyasına ekleyin:
```
nano .env
```
En alt satıra ekleyin:
```
PRIVATE_KEY="0x..."
```
0x... yerine kopyaladığınız private key'i yapıştırın. Kaydedin: Ctrl+X → Y → Enter
# Sonra tanıtın:
```
source .env
```
# 💧 Adım 5 — Testnet USDC Alın

faucet.circle.com adresine gidin
Network olarak Arc Testnet seçin 

Gönderin — birkaç saniyede USDC gelir


# 📝 Adım 6 — Kontratı Yazın
Örnek dosyayı silin:
```
rm src/Counter.sol
```
# Yeni dosya oluşturun:
```
touch src/ArcGreeter.sol
nano src/ArcGreeter.sol
```
# Aşağıdaki kodu yapıştırın:
```
javascript// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract ArcGreeter {
    string private message;

    event MessageUpdated(string newMessage);

    constructor() {
        message = "Hello Arc Testnet!";
    }

    function setMessage(string memory newMessage) public {
        message = newMessage;
        emit MessageUpdated(newMessage);
    }

    function getMessage() public view returns (string memory) {
        return message;
    }
}
```
Kaydedin: Ctrl+X → Y → Enter

# 🧪 Adım 7 — Testleri Yazın
```
rm -rf script
rm test/Counter.t.sol
touch test/ArcGreeter.t.sol
nano test/ArcGreeter.t.sol
```
# Yapıştırın:
```
javascript// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import "forge-std/Test.sol";
import "../src/ArcGreeter.sol";

contract ArcGreeterTest is Test {
    ArcGreeter arcGreeter;

    function setUp() public {
        arcGreeter = new ArcGreeter();
    }

    function testInitialMessage() public view {
        string memory expected = "Hello Arc Testnet!";
        string memory actual = arcGreeter.getMessage();
        assertEq(actual, expected);
    }

    function testSetMessage() public {
        string memory newMessage = "Merhaba Arc!";
        arcGreeter.setMessage(newMessage);
        string memory actual = arcGreeter.getMessage();
        assertEq(actual, newMessage);
    }

    function testMessageUpdatedEvent() public {
        string memory newMessage = "Building on Arc!";
        vm.expectEmit(true, true, true, true);
        emit ArcGreeter.MessageUpdated(newMessage);
        arcGreeter.setMessage(newMessage);
    }
}
```
Kaydedin: Ctrl+X → Y → Enter

# ✅ Adım 8 — Test Edin ve Derleyin
```
forge test
```
Her satırda [PASS] görmelisiniz. Sonra derleyin:
```
forge build
```
Hata yoksa devam edin.

# 🚀 Adım 9 — Deploy Edin
```forge create src/ArcGreeter.sol:ArcGreeter \
  --rpc-url $ARC_TESTNET_RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```
Başarılı olursa:
Deployer:         0xABC123...

Deployed to:      0xDEF456...

Transaction hash: 0xGHI789...

"Deployed to:" adresini kopyalayın ve .env'e ekleyin:
```
nano .env
```
En alta ekleyin:
```
ARCGREETER_ADDRESS="0x..."
```
# Kaydedin ve tanıtın:
```
source .env
```
# 📖 Adım 10 — Kontratı Okuyun
```
cast call $ARCGREETER_ADDRESS "getMessage()(string)" \
  --rpc-url $ARC_TESTNET_RPC_URL
```
"Hello Arc Testnet!" dönüyorsa başarılı!

# 🔍 Adım 11 — Explorer'da Doğrulayın
Tarayıcıdan testnet.arcscan.app adresine gidin. Deploy sırasında gördüğünüz Transaction hash değerini arama kutusuna yapıştırın.

