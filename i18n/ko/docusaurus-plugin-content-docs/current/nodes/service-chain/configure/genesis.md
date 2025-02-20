# 제네시스 파일

이 페이지는 `genesis.json` 파일에 대한 자세한 내용을 설명합니다.

## 제네시스 JSON 파일 구조 <a id="genesis-json-file-structure"></a>

`genesis.json` 파일 구조는 다음 표에 설명되어 있습니다.

| 필드 이름 | 설명 |
| ------------------------------ | ----------------------------- |
| config | 블록체인 구성입니다. [Config](#config) 섹션을 참조하세요. |
| nonce | (사용되지 않음) 이 필드는 이더리움에서 파생되었지만 Klaytn에서는 사용되지 않습니다. |
| timestamp | 블록이 생성된 유닉스 시간입니다. |
| extraData | 서명자 vanity와 유효성 검사자 목록, 제안자 씰, 커밋 씰을 포함하는 RLP 인코딩된 Istanbul 추가 데이터를 위한 데이터 결합 필드입니다. |
| gasLimit | 블록에 사용된 최대 가스 양입니다. |
| difficulty | (사용되지 않음) 이 필드는 이더리움에서 파생되었지만 Klaytn에서는 사용되지 않습니다. |
| mixhash | (사용되지 않음) 이 필드는 이더리움에서 파생되었지만 Klaytn에서는 사용되지 않습니다. |
| coinbase | miner가 보상을 받을 주소입니다. 이 필드는 Clique 합의 엔진에만 사용됩니다. |
| alloc | 미리 정의된 계정. |
| number | 블록 번호 필드입니다. |
| gasUsed | 블록에 사용된 가스 양입니다. |
| parentHash | 이전 블록의 해시 값입니다. |

### Config <a id="config"></a>

`config` 필드에는 체인 관련 정보가 저장됩니다.

| 필드 이름 | 설명 |
| ------------------------------ | ----------------------------- |
| chainId | 현재 체인을 식별하며 리플레이 공격을 방지하는 데 사용됩니다. |
| istanbulCompatibleBlock | Istanbul 변경이 적용되는 블록 번호입니다. |
| istanbul, clique | 합의 엔진의 유형입니다. |
| unitPrice | 단가. |
| deriveShaImpl | 트랜잭션 해시 및 영수증 해시를 생성하는 메서드를 정의합니다. |
| governance | 네트워크의 거버넌스 정보입니다. [governance](#governance) |


### extraData <a id="extradata"></a>

`extraData` 필드는 제안자 vanity와 RLP로 인코딩된 Istanbul 추가 데이터의 연결입니다:

  - 제안자 vanity는 임의의 제안자 vanity 데이터를 포함하는 32바이트 데이터입니다.
  - 나머지 데이터는 RLP로 인코딩된 Istanbul 추가 데이터입니다:
     - 검증자: 오름차순으로 나열된 검증자 목록입니다.
     - Seal: 헤더의 제안자 서명입니다. `genesis.json`의 경우 65개의 `0x0`으로 초기화된 바이트 배열입니다.
     - CommittedSeal: 합의 증명을 위한 커미션 서명 씰 목록입니다. `genesis.json`의 경우 빈 배열입니다.

**예제**
| 필드 | 유형 | value |
| ----- | ---- | ----- |
| vanity | 32바이트 16진수 문자열 | 0x00000000000000000000000000000000000000000000000000000000 |
| Validators | []address | [0x48009b4e20ec72aadf306577cbe2eaf54b0ebb16,0x089fcc42fd83baeee4831319375413b8bae3aceb] |
| Seal | 65개 요소의 바이트 배열 | [0x0,...,0x0] |
| CommittedSeal | [][]byte | [] |

위 데이터가 포함된 `extraData`는 다음과 같이 생성됩니다.
```
concat('0x',Vanity,RLPEncode({Validators,Seal,CommittedSeal}))
```
여기서 `concat`은 문자열 연결 함수이고, `RLPEncode`는 주어진 구조를 RLP 인코딩된 문자열로 변환하는 함수입니다.

With this function, the output `extraData` for this example is 0x0000000000000000000000000000000000000000000000000000000000000000f86fea9448009b4e20ec72aadf306577cbe2eaf54b0ebb1694089fcc42fd83baeee4831319375413b8bae3acebb8410000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000c0.


## 합의 엔진 <a id="consensus-engine"></a>

클레이튼 네트워크에서 사용할 수 있는 합의 엔진은 Clique와 Istanbul입니다. 각 엔진에 대한 설명은 다음과 같습니다.

### Clique <a id="clique"></a>

`clique` 필드에는 권한 증명(POA) 기반 씰링에 대한 구성이 저장됩니다.

| 필드 | 설명
| ----------------------- | ---------------------------- |
| period | 연속된 블록 사이의 최소 시간 간격(단위: 초). |
| epoch | 투표를 초기화하고 체크포인트로 표시할 블록 수입니다. |

### Istanbul <a id="istanbul"></a>

`Istanbul` 필드에는 Istanbul 기반 씰링에 대한 구성이 저장됩니다.

| 필드 | 설명 |
| ----------------------- | ----------------------------- |
| epoch | 투표를 체크포인트로 재설정할 블록 수입니다. |
| policy | 블록 제안자 선택 정책입니다. [0: 라운드 로빈, 1: 고정, 2: 가중치 무작위] |
| sub | 위원회 규모. |

## Governance <a id="governance"></a>

`governance` 필드에는 네트워크에 대한 거버넌스 정보가 저장됩니다.

| 필드 | 설명
| ----------------------- | ----------------------------- |
| governanceMode | 세 가지 거버넌스 모드 중 하나입니다. [`none`, `single`, `ballot`] |
| governingNode | 지정된 거버넌스 노드의 주소입니다. 거버넌스 모드가 `single`인 경우에만 작동합니다.  |
| reward | 리워드 설정을 저장합니다. [Reward](#reward) 섹션을 참고하세요.  |

### Reward <a id="reward"></a>

`reward` 필드에는 네트워크의 토큰 이코노미에 대한 정보가 저장됩니다.

| 필드 | 설명
| ----------------------- | ----------------------------- |
| mintingAmount | 블록이 생성될 때 발행되는 peb의 양입니다. 값은 큰따옴표로 묶어야 합니다. |
| ratio | `CN/KIR/PoC`에 대한 분배 비율을 `/`로 구분합니다. 모든 값의 합계는 100이어야 합니다.  |
| useGiniCoeff | Gini계수 사용 여부. |
| deferredTxFee | 블록에 대한 TX 수수료를 분배하는 방법. |
| stakingUpdateInterval | 스테이킹 정보를 업데이트할 블록 높이의 시간 간격입니다. |
| proposerUpdateInterval | 블록 높이에서 제안자 정보를 업데이트하는 시간 간격입니다. |
| minimumStake | 코어 셀 오퍼레이터에 참여할 수 있는 최소 지분량입니다. |

## 예제 <a id="example"></a>

```
{
    "config": {
        "chainId": 2019,
        "istanbulCompatibleBlock": 0,
        "istanbul": {
            "epoch": 604800,
            "policy": 2,
            "sub": 13
        },
        "unitPrice": 25000000000,
        "deriveShaImpl": 2,
        "governance": {
            "governingNode": "0x46b0bd6380005952759f605d02a6365552c776f3",
            "governanceMode": "single",
            "reward": {
                "mintingAmount": 6400000000000000000,
                "ratio": "50/40/10",
                "useGiniCoeff": true,
                "deferredTxFee": true,
                "stakingUpdateInterval": 86400,
                "proposerUpdateInterval": 3600,
                "minimumStake": 5000000
            }
        }
    },
    "nonce": "0x0",
    "timestamp": "0x5c9af60e",
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000f89af85494aeae0ab623d4118ac62a2decc386949b5ce67ce29446b0bd6380005952759f605d02a6365552c776f394699b607851c878e29499672f42a769b71f74be8e94e67598eb5831164574c876994d53f63eab4f36d7b8410000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000c0",
    "gasLimit": "0xe8d4a50fff",
    "difficulty": "0x1",
    "mixHash": "0x63746963616c2062797a616e74696e65206661756c7420746f6c6572616e6365",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
        "0000000000000000000000000000000000000400": {
            "code": "0x6080604052600436106101505763ffffffff60e00a165627a7a7230582093756fe617053766b158f7c64998c746eb38f0d5431cc50231cc9fb2cd1fd9950029",
            "balance": "0x0"
        },
        "46b0bd6380005952759f605d02a6365552c776f3": {
            "balance": "0x446c3b15f9926687d2c40534fdb564000000000000"
        },
        "699b607851c878e29499672f42a769b71f74be8e": {
            "balance": "0x446c3b15f9926687d2c40534fdb564000000000000"
        },
        "aeae0ab623d4118ac62a2decc386949b5ce67ce2": {
            "balance": "0x446c3b15f9926687d2c40534fdb564000000000000"
        },
        "e67598eb5831164574c876994d53f63eab4f36d7": {
            "balance": "0x446c3b15f9926687d2c40534fdb564000000000000"
        }
    },
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```
