# PICT лаб

Нэр: ...
Код: ...

# Хэсэг A: Category-Partition

## Давуу эрэмбэ

Нэг хүсэлтэд хэд хэдэн алдаа зэрэг байвал `from → to → amount` дарааллаар эхний алдаа буцна, учир нь энэ нь тодорхойлолтын алдааны жагсаалтын дараалалтай ижил бөгөөд аливаа дараагийн шалгалт өмнөх шалгалтын үр дүнд тулгуурладаг.

Дараалал: ERROR_NO_FROM → ERROR_FROZEN → ERROR_NO_TO → ERROR_SAME_ACCOUNT → ERROR_BAD_AMOUNT → ERROR_INSUFFICIENT → ERROR_LIMIT

## A1: Сонголт ба төлөөлөх утгууд

| Параметр | Сонголт | Төлөөлөх утгууд |
|---|---|---|
| from | Дансны төлөв | Valid (хүчинтэй), Frozen (царцаагдсан), Missing (байхгүй) |
| to | Дансны төлөв | Valid, Missing |
| to | from-той харьцаа | Valid (from-оос өөр), SameAsFrom (from-тэй ижил) |
| amount | Хэмжээ | Zero (0 ба сөрөг дүн), Normal, OverBalance, OverLimit |
| currency | Данстай харьцаа | Same, Different |

PICT-ийн `To` параметр нь дээрх хоёр сонголтыг нэгтгэж `Valid, Missing, SameAsFrom` гэсэн 3 утгатай болно.

## Далд орчны сонголтууд

Хүсэлтэд харагдахгүй ч үр дүнд нөлөөлнэ.

| Орчин | Утгууд | Яагаад чухал вэ |
|---|---|---|
| Өдрийн лимит хэр дүүрсэн | Хоосон, Лимитэд ойрхон | amount өөрөө лимитээс бага ч өмнөх шилжүүлэгтэй нийлээд хэтэрч болно |
| Дансны үлдэгдэл | Хангалттай, Хязгаарт ойрхон | OverBalance-ийг тодорхойлно |
| Ханш (currency Different үед) | Тогтмол ханш | Хөрвүүлсний дараах дүнгээр шалгах тул үр дүнг эргүүлж болно |

## A2: Хязгаарлалтууд

| Параметр | Утга | Тэмдэг | Үндэслэл |
|---|---|---|---|
| From | Valid | - | Хэвийн үндсэн утга. |
| From | Frozen | [ERROR] | Царцаагдсан данснаас шилжүүлэхэд ERROR_FROZEN буцна. |
| From | Missing | [ERROR] | Байхгүй данс ERROR_NO_FROM буцаана. |
| To | Valid | - | Хэвийн үндсэн утга. |
| To | Missing | [ERROR] | Байхгүй данс ERROR_NO_TO буцаана. |
| To | SameAsFrom | [ERROR][SINGLE] | ERROR_SAME_ACCOUNT буцаах бөгөөд ижил данс байхад currency-н ялгаа утгагүй тул ганц удаа шалгахад хангалттай. |
| Amount | Normal | - | Хэвийн үндсэн утга. |
| Amount | Zero | [ERROR][SINGLE] | ERROR_BAD_AMOUNT буцах бөгөөд дүн нь currency-аас үл хамаарна. |
| Amount | OverBalance | [ERROR] | ERROR_INSUFFICIENT буцаана. |
| Amount | OverLimit | [ERROR] | ERROR_LIMIT буцаана. |
| Currency | Same | - | Хөрвүүлэлтгүй үндсэн тохиолдол. |
| Currency | Different | [IF/property] | Хөрвүүлсний дараах дүнгээр шалгадаг тул зөвхөн Amount-ийн алдаатай хосолбол утга учиртай. |

### Тоо: 72 → 14

Бүрэн олонлог 3 × 3 × 4 × 2 = 72. Хязгаарлалтын дараа: happy path 2, [ERROR] (From 2 + To 1 + Amount 2 = 5 утга) × Currency 2 = 10, [SINGLE] 2 (Zero, SameAsFrom). Нийт 2 + 10 + 2 = 14.
