# 디지털 도무송 스티커 임포지션 API

디지털 도무송 스티커에 대한 임포지션을 하려면 다음의 주소로 `POST` Http요청을 보내면 됩니다.

`http://overlord.dtp21.com/api/v1/queue/push-imposition`

입력데이터는 `JSON`형식으로 다음과 같습니다. Ajax로 전송시 `Content-Type: application/json; charset=utf-8` 으로 지정되어야 합니다.

```Javascript
var params = {
    // 밑의 항목에서 자세하게 설명합니다. [필수]
    "templateUrl": "https://s3.ap-northeast-2.amazonaws.com/overlord-res/templates/135.tem",

    // 밑의 항목에서 자세하게 설명합니다. [필수]
    "callbackUrl": "http://overlord.dtp21.com/api/v1/test/batch-callback",

    // 판번호 [필수]
    "number": "PPTT-170627-104401",
    
    // 판매수 []
    "sheets": 1,

    // 타이틀 텍스트: 완성된 PDF의 구석 텍스트영역에 나오는 항목입니다. [필수]
    "title": "PPTT-170627-104401 디지털도무송-4도-A3",

    // 바코드내용 [필수]
    "barcodeText": "PPTT-170627-104401",

    // "general-pan-pdf" 값이 고정입니다. [필수]
    "panBucketName": "general-pan-pdf",

    // 임포지션할 주문파일정보 [필수]
    "items": [{
        // 주문번호 (SAGA에서 쓸 때는 식별할 수 있는 유일한 값을 넣어주면 됩니다.) [필수]
        "uid": "pr170622021301",

        // 장수 [필수]
        "sheets": 100,

        // 하리꼬미할 PDF주소 (Http URL) [필수]
        "pdfUrl": "http://overlord-order-pdf.s3.ap-northeast-2.amazonaws.com/3FED/pr170622021301_korea_24ea.pdf",

        // 실제파일이름 (pdfUrl의 마지막부분을 넣으면 됩니다.) [필수]
        "fileName": "pr170622021301_korea_24ea.pdf",

        // 편집사이즈 가로 [필수]
        "editWidth": 92.0,
        // 편집사이즈 세로 [필수]
        "editHeight": 52.0,
        // 재단마진 [필수]
        "cutMargin": 1.0,

        // PDF가 여러페이지일때 이 항목의 시작페이지 [필수]
        "startPageNumber": 1
    }],

    // 앞면도수 [필수]
    "frontColor": 4,
    // 뒷면도수 (뒷면이 없으면 0, 도무송일경우 1) [필수]
    "backColor": 4,

    // 건당 페이지카운트 (단면은 1, 양면은 2, 도무송도 2) [필수]
    "pageCount": 2,

    // 뒷면을 EPS파일로 변환함 (도무송일때는 true) [필수]
    "convertBackSideToEps": true,

    // 지도판을 만든다. (현재 사용되지 않음) [무시]
    "writeMap": false,
    // 라벨을 만든다. (현재 사용되지 않음) [무시]
    "writeLabel": false,
    // 리포트를 만든다. (현재 사용되지 않음) [무시]
    "writeReport": false,
}

// CORS를 사용하기 때문에 서버에서 전송하는게 아니라면 미리 오버로드쪽에 도메인을 등록해야 합니다.
// jch0220@gmail.com 으로 연락주시면 됩니다.
var url = 'http://overlord.dtp21.com/api/v1/queue/push-imposition';
$.ajax({
    url: url,
    dataType: "json",
    contentType: "application/json;charset=utf-8",
    type: "POST",
    xhrFields: { withCredentials: true }, // Ajax CORS를 위해 필수입니다. 서버에서 요청할때는 없어도 됩니다.
    data: JSON.stringify(params),
    success: function (resp) {
        alert(resp.message);
    }
});

```

- - -
- - -

## URL관련 전송데이터 설명

### `templateUrl`: string(http url)
판PDF만들때 쓰일 템플릿 파일의 주소입니다.

`https://s3.ap-northeast-2.amazonaws.com/overlord-res/templates/<번호>.tem`

<번호> 부분에 다음 항목중에 하나를 넣으면 됩니다.

 * `61`: 디지탈 315x464
 * `75`: 디지탈전단-464X315(A3)
 * `87`: 디지털 도무송-엽서(A3-297x420)
 * `88`: 디지털 도무송-엽서(A4-210x297)
 * `102`: 디지털(중국)-명함(92x52)-315x464
 * `103`: 디지털(중국)-명함(92x56)-315x464
 * `106`: 디지탈전단-464X315(A3)-레이저
 * `3`: 디지탈-444X297(A3)
 * `59`: 디지탈-464X315(A3)
 * `131`: 디지털 도무송(A3-420x297)
 * `135`: 디지털 도무송(A3-297x420)
 * `136`: 디지털 도무송(A4-210x297)
 * `148`: 디지탈-444X297(A3-92x55)
 * `149`: 디지탈-444X297(A3-85x55)
 * `92`: 디지털(중국)-명함(92x52)-297x420
 * `93`: 디지털(중국)-명함(88x54)-297x420
 * `73`: 디지털 MC-500x300
 * `172`: 필리핀 디지털 도무송 스티커(대지:317×469, 내용:292×449)
 * `173`: 필리핀 디지털 명함(대지: 317×469, 24up)



### `callbackUrl`: string(http url)

판생성이 완료되면 `callbackUrl`로 `POST` Http요청이 전송됩니다. 이떄 전송되는 항목은 다음과 같습니다.

```Javascript
// 성공했을때
{
    // Request할때 넣었던 판번호
    "number": "PPTT-170627-104401",

    // 실패여부 (성공)
    "isFailed": false,

    // 판PDF와 도무송칼 EPS
    "fileUrls": [
        { "kind": "pan", "url": "http://www.dddd.com/alskdjflaskdf.pdf" }
        { "kind": "sg-sc", "url": "http://www.dddd.com/alskdjflaskdf.sg-sc.pdf" }
    ]
}

// 실패했을때
{
    // Request할때 넣었던 판번호
    "number": "PPTT-170627-104401",

    // 실패여부 (실패)
    "isFailed": true,

    // 에러항목 (배열이지만 보통 하나만 있음)
    "errorItems": [
        { "message": "입력 PDF 파일 불량" }
    ]
}

```
