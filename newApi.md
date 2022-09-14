# New 임포지션 API

디지털 제품에 대한 임포지션을 하려면 다음의 주소로 POST Http요청을 보내면 됩니다.

`https://www.ondtp.com/api/v2/pan/build`

입력데이터는 `JSON`형식으로 다음과 같습니다. Ajax로 전송시 `Content-Type: application/json; charset=utf-8` 으로 지정되어야 합니다.


```js
var input = {
    // 완료되었을때 호출되는 URL [필수]: 밑의 항목에서 자세하게 설명합니다.
    callbackUrl: '',
    
    // 오버로드주문여부 [필수]: 오버로드주문 으로 만드는판이면 true, vicut에서는 false 고정 
    isOverlord: false,
    
    // 사이트코드 [필수] vicut 사이트코드는 harikomi 고정
    siteCode: 'harikomi',


    impositionProps: {
    
        // 독판여부 [필수] 그냥 true
        singlePanMode: true,
        
        // 판번호 [필수]
        number: 'PPTT-220911-120451',
        
        // 바코드내용 [필수]
        barcodeText: 'PPTT-220911-120451', 

        // 타이틀 텍스트 [필수]: 완성된 PDF의 구석 텍스트영역에 나오는 항목입니다. 
        title: 'PPTT-170627-104401 디지털도무송-4도-A3', 

        // 템플릿 번호 [필수]: 밑의 항목에서 자세하게 설명합니다. 
        templateId: 135, 

        // 판출고일, 그냥 판생성일 넣으면 됩니다..
        expectedDoneDate: '2022-09-22', 

        // 아이템배치형식 [필수]  0: auto, 11: fitToGrid
        arrangeKind: 11, 

        // 인쇄방식 [필수]  0: none, 10: offset, 20: master, 30: digital
        printingKind: 30, 

        // 판 매수 [필수] 독판이므로 items 항목의 sheets 값과 같아야한다.
        sheets: 200, 

        // 우선순위: 그냥 고정값을 넣으면 됩니다.
        priorityCode: "PR_01",
        priorityText: "보통",

        // 임포지션할 주문파일정보 [필수]
        items: [{
        
            // 주문번호 (SAGA에서 쓸 때는 식별할 수 있는 유일한 값을 넣어주면 됩니다.) [필수]
            uid: "pr170622021301",

            // 장수 [필수]
            sheets: 200,

            // 하리꼬미할 PDF주소 (Http URL) [필수]
            pdfUrl: "https://overlord-order-pdf.s3.ap-northeast-2.amazonaws.com/3FED/pr170622021301_korea_24ea.pdf",

            // 실제파일이름 (pdfUrl의 마지막부분을 넣으면 됩니다.) [필수]
            fileName: 'pr170622021301_korea_1ea.pdf',

            // 편집사이즈 가로 [필수]
            editWidth: 92,

            // 편집사이즈 세로 [필수]
            editHeight: 52,

            // 재단마진 [필수]
            cutMargin: 1,

            // PDF가 여러페이지일때 이 항목의 시작페이지 [필수]
            startPageNumber: 1,

            // 단면: 1, 양면: 2
            pageCount: 2, 
        }]
    }
}

var jwt = '';  // vicut의 liveKey를 넣어주세요

var url = 'https://www.ondtp.com/api/v2/pan/build';

// jquery일때
$.ajax({
    url: url,
    dataType: "json",
    contentType: "application/json;charset=utf-8",
    headers: {"Authorization": "Bearer " + jwt}, // jwt추가
    type: "POST",
    // withCredentials 이 true로 있으면 안됩니다. false이거나 아예 제거해야합니다.
    //xhrFields: { withCredentials: false }, 
    data: JSON.stringify(params),

    success: function (result) {
        alert('판 임포지션이 요청되었습니다. - #' + result.id);
    }
});

// fetch일때
fetch(url, {
    method: 'POST',
    body: JSON.stringify(params),
    headers: {
        "Content-Type": "application/json;charset=utf-8",
        "Authorization": "Bearer " + jwt
    }
})
.then(resp => resp.json())
.then(result => {
    alert('판 임포지션이 요청되었습니다. - #' + result.id);
});
```

- - -

### TemplateId 
 * `502`: 디지털(CC-330) 297×420(A3) 93×57-21ea
 * `225`: 디지털(CC-330) 297×420(A3) 91×53-21ea
 * `345`: 디지털(CC-330) 297×420(A3) 150×102-8ea 
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

```js
// 성공했을때
{
    // Request할때 넣었던 판번호
    "number": "PPTT-170627-104401",

    // 실패여부 (성공)
    "isFailed": false,

    // 판PDF와 도무송칼 EPS
    "fileUrls": [
        { "kind": "pan", "url": "https://www.dddd.com/alskdjflaskdf.pdf" }
        { "kind": "sg-sc", "url": "https://www.dddd.com/alskdjflaskdf.sg-sc.pdf" }
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
