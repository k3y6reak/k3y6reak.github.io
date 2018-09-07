---
layout: post
title:  "Hackingcamp 2018 eaJsy"
subtitle: "Hackingcamp 2018 eaJsy 풀이 입니다."
date:   2018-09-07 15:33:00 +0900
categories: ['ctfs']
comments: true
---

## 풀이

eaJsy는 html 파일로 코드를 살펴보면 아래와 같다.

{% highlight javascript %}
<html><head><script>eval(function(p,a,c,k,e,d){while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+c+'\\b','g'),k[c])}}return p}('5 6(0){8 4=2 1().3();7(2 1().3()<4+0){}}',9,9,'delay|Date|new|getTime|start|function|sleep|while|var'.split('|')));eval(function(p,a,c,k,e,d){while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+c+'\\b','g'),k[c])}}return p}('0(1(2("3")))',4,4,'eval|decodeURI|atob|ZXZhbChmdW5jdGlvbihwLGEsYyxrLGUsZCklN0JlPWZ1bmN0aW9uKGMpJTdCcmV0dXJuKGMlM0NhPycnOmUoYy9hKSkrU3RyaW5nLmZyb21DaGFyQ29kZShjJTI1YSsxNjEpJTdEO3doaWxlKGMtLSklN0JpZihrJTVCYyU1RCklN0JwPXAucmVwbGFjZShuZXclMjBSZWdFeHAoZShjKSwnZycpLGslNUJjJTVEKSU3RCU3RHJldHVybiUyMHAlN0QoJyVDMiVBMSUyMCVDMiVCMj0lN0IlQzIlQTI6JTIyJUMzJTgxKy89JTIyLCVDMyU4MDolQzIlQUQoZSklN0IlQzIlQTElMjAlQzIlQTI9JTIyJUMyJUI5JTIyKyVDMiVCQi4lQzIlQTIrJTIySyUyMjslQzIlQTElMjB0PSUyMiUyMjslQzIlQTElMjBuLHIsaSxzLG8sdSxhOyVDMiVBMSUyMGY9MDtlPSVDMiVCMi4lQzIlQjgoZSk7JUMyJUIxKGYlM0NlLiVDMiVCMCklN0JuPWUuJUMyJUE1KGYrKyk7cj1lLiVDMiVBNShmKyspO2k9ZS4lQzIlQTUoZisrKTtzPW4lM0UlM0UyO289KG4mMyklM0MlM0M0JTdDciUzRSUzRTQ7dT0ociYlQzIlQjMpJTNDJTNDMiU3Q2klM0UlM0U2O2E9aSYlQzIlQTg7JUMyJUE3KCVDMiVCQShyKSklN0J1PWE9JUMyJUFFJTdEJUMyJUFBJTIwJUMyJUE3KCVDMiVCQShpKSklN0JhPSVDMiVBRSU3RHQ9dCslQzIlQTIuJUMyJUE2KHMpKyVDMiVBMi4lQzIlQTYobykrJUMyJUEyLiVDMiVBNih1KSslQzIlQTIuJUMyJUE2KGEpJTdEJUMyJUFGJTIwdCU3RCwlQzMlODI6JUMyJUFEKGUpJTdCJUMyJUExJTIwJUMyJUEyPSUyMiVDMiVCOSUyMislQzIlQkIuJUMyJUEyKyUyMkslMjI7JUMyJUI3LiVDMyU4Mz0lMjIlQzMlODQ6Ly8lQzMlODUuJUMyJUJFLyUyMjslQzIlQTElMjB0PSUyMiUyMjslQzIlQjcuJUMyJUJGKCk7JUMyJUExJTIwbixyLGk7JUMyJUExJTIwcyxvLHUsYTslQzIlQTElMjBmPTA7ZT1lLiVDMiVCNCgvJTVCJTVFQS0lQzMlOTEtJUMzJThELTkrLz0lNUQvZywlMjIlMjIpOyVDMiVCMShmJTNDZS4lQzIlQjApJTdCcz0lQzIlQTIuJUMyJUFDKGUuJUMyJUE2KGYrKykpO289JUMyJUEyLiVDMiVBQyhlLiVDMiVBNihmKyspKTt1PSVDMiVBMi4lQzIlQUMoZS4lQzIlQTYoZisrKSk7YT0lQzIlQTIuJUMyJUFDKGUuJUMyJUE2KGYrKykpO249cyUzQyUzQzIlN0NvJTNFJTNFNDtyPShvJiVDMiVCMyklM0MlM0M0JTdDdSUzRSUzRTI7aT0odSYzKSUzQyUzQzYlN0NhO3Q9dCslQzIlQTQuJUMyJUEzKG4pOyVDMiVBNyh1IT0lQzIlQUUpJTdCdD10KyVDMiVBNC4lQzIlQTMociklN0QlQzIlQTcoYSE9JUMyJUFFKSU3QnQ9dCslQzIlQTQuJUMyJUEzKGkpJTdEJTdEdD0lQzIlQjIuJUMyJUJDKHQpOyVDMyU4RSglQzMlOEYpOyVDMiVBRiUyMHQlN0QsJUMyJUI4OiVDMiVBRChlKSU3QmU9ZS4lQzIlQjQoLyVDMyU5MC9nLCUyMm4lMjIpOyVDMiVBMSUyMHQ9JTIyJTIyOyVDMyU4NiglQzIlQTElMjBuPTA7biUzQ2UuJUMyJUIwO24rKyklN0IlQzIlQTElMjByPWUuJUMyJUE1KG4pOyVDMiVBNyhyJTNDJUMyJUE5KSU3QnQrPSVDMiVBNC4lQzIlQTMociklN0QlQzIlQUElMjAlQzIlQTcociUzRSVDMyU4QyYmciUzQyVDMyU4QiklN0J0Kz0lQzIlQTQuJUMyJUEzKHIlM0UlM0U2JTdDJUMzJTg3KTt0Kz0lQzIlQTQuJUMyJUEzKHImJUMyJUE4JTdDJUMyJUE5KSU3RCVDMiVBQSU3QnQrPSVDMiVBNC4lQzIlQTMociUzRSUzRSVDMiVCNSU3QyVDMiVCRCk7dCs9JUMyJUE0LiVDMiVBMyhyJTNFJTNFNiYlQzIlQTglN0MlQzIlQTkpO3QrPSVDMiVBNC4lQzIlQTMociYlQzIlQTglN0MlQzIlQTkpJTdEJTdEJUMyJUFGJTIwdCU3RCwlQzIlQkM6JUMyJUFEKGUpJTdCJUMyJUExJTIwdD0lMjIlMjI7JUMyJUExJTIwbj0wOyVDMiVBMSUyMHI9JUMzJTg4PSVDMiVBQj0wOyVDMiVCMShuJTNDZS4lQzIlQjApJTdCcj1lLiVDMiVBNShuKTslQzIlQTcociUzQyVDMiVBOSklN0J0Kz0lQzIlQTQuJUMyJUEzKHIpO24rKyU3RCVDMiVBQSUyMCVDMiVBNyhyJTNFJUMzJTg5JiZyJTNDJUMyJUJEKSU3QiVDMiVBQj1lLiVDMiVBNShuKzEpO3QrPSVDMiVBNC4lQzIlQTMoKHImJUMzJThBKSUzQyUzQzYlN0MlQzIlQUImJUMyJUE4KTtuKz0yJTdEJUMyJUFBJTdCJUMyJUFCPWUuJUMyJUE1KG4rMSk7JUMyJUI2PWUuJUMyJUE1KG4rMik7dCs9JUMyJUE0LiVDMiVBMygociYlQzIlQjMpJTNDJTNDJUMyJUI1JTdDKCVDMiVBQiYlQzIlQTgpJTNDJTNDNiU3QyVDMiVCNiYlQzIlQTgpO24rPTMlN0QlN0QlQzIlQUYlMjB0JTdEJTdEJyw0OSw0OSwndmFyJTdDXzAlN0Nmcm9tQ2hhckNvZGUlN0NTdHJpbmclN0NjaGFyQ29kZUF0JTdDY2hhckF0JTdDaWYlN0M2MyU3QzEyOCU3Q2Vsc2UlN0NjMiU3Q2luZGV4T2YlN0NmdW5jdGlvbiU3QzY0JTdDcmV0dXJuJTdDbGVuZ3RoJTdDd2hpbGUlN0NoYyU3QzE1JTdDcmVwbGFjZSU3QzEyJTdDYzMlN0Nsb2NhdGlvbiU3Q18xJTdDekJERUZDR0hJSiU3Q2lzTmFOJTdDdGhpcyU3Q18yJTdDMjI0JTdDb3JnJTdDcmVsb2FkJTdDZW5jb2RlJTdDTE1OT1BRUlNUVVZXWFlaYWJjZGVmZ2hpamtsbW5vcHFyc3R1dnd4eTAxMjM0NTY3ODlBJTdDZGVjb2RlJTdDaHJlZiU3Q2h0dHAlN0NoYWNraW5nY2FtcCU3Q2ZvciU3QzE5MiU3Q2MxJTdDMTkxJTdDMzElN0MyMDQ4JTdDMTI3JTdDejAlN0NzbGVlcCU3QzEwMDAwMDAwMCU3Q3JuJTdDWmEnLnNwbGl0KCclN0MnKSkp'.split('|')))
hc.decode("TFOBUWB9Z5W1eG+uZkZ2Y4CvaC+rd5BiZ4u+")</script></head></html>
{% endhighlight %}


한 눈에 확인하기 어렵기 때문에 `http://jsbeautifier.org`에서 위 코드를 보기 좋게 변환한다.

{% highlight javascript %}
function sleep(delay) {
    var start = new Date().getTime();
    while (new Date().getTime() < start + delay) {}
}
eval(decodeURI(atob("ZXZhbChmdW5jdGlvbihwLGEsYyxrLGUsZCklN0JlPWZ1bmN0aW9uKGMpJTdCcmV0dXJuKGMlM0NhPycnOmUoYy9hKSkrU3RyaW5nLmZyb21DaGFyQ29kZShjJTI1YSsxNjEpJTdEO3doaWxlKGMtLSklN0JpZihrJTVCYyU1RCklN0JwPXAucmVwbGFjZShuZXclMjBSZWdFeHAoZShjKSwnZycpLGslNUJjJTVEKSU3RCU3RHJldHVybiUyMHAlN0QoJyVDMiVBMSUyMCVDMiVCMj0lN0IlQzIlQTI6JTIyJUMzJTgxKy89JTIyLCVDMyU4MDolQzIlQUQoZSklN0IlQzIlQTElMjAlQzIlQTI9JTIyJUMyJUI5JTIyKyVDMiVCQi4lQzIlQTIrJTIySyUyMjslQzIlQTElMjB0PSUyMiUyMjslQzIlQTElMjBuLHIsaSxzLG8sdSxhOyVDMiVBMSUyMGY9MDtlPSVDMiVCMi4lQzIlQjgoZSk7JUMyJUIxKGYlM0NlLiVDMiVCMCklN0JuPWUuJUMyJUE1KGYrKyk7cj1lLiVDMiVBNShmKyspO2k9ZS4lQzIlQTUoZisrKTtzPW4lM0UlM0UyO289KG4mMyklM0MlM0M0JTdDciUzRSUzRTQ7dT0ociYlQzIlQjMpJTNDJTNDMiU3Q2klM0UlM0U2O2E9aSYlQzIlQTg7JUMyJUE3KCVDMiVCQShyKSklN0J1PWE9JUMyJUFFJTdEJUMyJUFBJTIwJUMyJUE3KCVDMiVCQShpKSklN0JhPSVDMiVBRSU3RHQ9dCslQzIlQTIuJUMyJUE2KHMpKyVDMiVBMi4lQzIlQTYobykrJUMyJUEyLiVDMiVBNih1KSslQzIlQTIuJUMyJUE2KGEpJTdEJUMyJUFGJTIwdCU3RCwlQzMlODI6JUMyJUFEKGUpJTdCJUMyJUExJTIwJUMyJUEyPSUyMiVDMiVCOSUyMislQzIlQkIuJUMyJUEyKyUyMkslMjI7JUMyJUI3LiVDMyU4Mz0lMjIlQzMlODQ6Ly8lQzMlODUuJUMyJUJFLyUyMjslQzIlQTElMjB0PSUyMiUyMjslQzIlQjcuJUMyJUJGKCk7JUMyJUExJTIwbixyLGk7JUMyJUExJTIwcyxvLHUsYTslQzIlQTElMjBmPTA7ZT1lLiVDMiVCNCgvJTVCJTVFQS0lQzMlOTEtJUMzJThELTkrLz0lNUQvZywlMjIlMjIpOyVDMiVCMShmJTNDZS4lQzIlQjApJTdCcz0lQzIlQTIuJUMyJUFDKGUuJUMyJUE2KGYrKykpO289JUMyJUEyLiVDMiVBQyhlLiVDMiVBNihmKyspKTt1PSVDMiVBMi4lQzIlQUMoZS4lQzIlQTYoZisrKSk7YT0lQzIlQTIuJUMyJUFDKGUuJUMyJUE2KGYrKykpO249cyUzQyUzQzIlN0NvJTNFJTNFNDtyPShvJiVDMiVCMyklM0MlM0M0JTdDdSUzRSUzRTI7aT0odSYzKSUzQyUzQzYlN0NhO3Q9dCslQzIlQTQuJUMyJUEzKG4pOyVDMiVBNyh1IT0lQzIlQUUpJTdCdD10KyVDMiVBNC4lQzIlQTMociklN0QlQzIlQTcoYSE9JUMyJUFFKSU3QnQ9dCslQzIlQTQuJUMyJUEzKGkpJTdEJTdEdD0lQzIlQjIuJUMyJUJDKHQpOyVDMyU4RSglQzMlOEYpOyVDMiVBRiUyMHQlN0QsJUMyJUI4OiVDMiVBRChlKSU3QmU9ZS4lQzIlQjQoLyVDMyU5MC9nLCUyMm4lMjIpOyVDMiVBMSUyMHQ9JTIyJTIyOyVDMyU4NiglQzIlQTElMjBuPTA7biUzQ2UuJUMyJUIwO24rKyklN0IlQzIlQTElMjByPWUuJUMyJUE1KG4pOyVDMiVBNyhyJTNDJUMyJUE5KSU3QnQrPSVDMiVBNC4lQzIlQTMociklN0QlQzIlQUElMjAlQzIlQTcociUzRSVDMyU4QyYmciUzQyVDMyU4QiklN0J0Kz0lQzIlQTQuJUMyJUEzKHIlM0UlM0U2JTdDJUMzJTg3KTt0Kz0lQzIlQTQuJUMyJUEzKHImJUMyJUE4JTdDJUMyJUE5KSU3RCVDMiVBQSU3QnQrPSVDMiVBNC4lQzIlQTMociUzRSUzRSVDMiVCNSU3QyVDMiVCRCk7dCs9JUMyJUE0LiVDMiVBMyhyJTNFJTNFNiYlQzIlQTglN0MlQzIlQTkpO3QrPSVDMiVBNC4lQzIlQTMociYlQzIlQTglN0MlQzIlQTkpJTdEJTdEJUMyJUFGJTIwdCU3RCwlQzIlQkM6JUMyJUFEKGUpJTdCJUMyJUExJTIwdD0lMjIlMjI7JUMyJUExJTIwbj0wOyVDMiVBMSUyMHI9JUMzJTg4PSVDMiVBQj0wOyVDMiVCMShuJTNDZS4lQzIlQjApJTdCcj1lLiVDMiVBNShuKTslQzIlQTcociUzQyVDMiVBOSklN0J0Kz0lQzIlQTQuJUMyJUEzKHIpO24rKyU3RCVDMiVBQSUyMCVDMiVBNyhyJTNFJUMzJTg5JiZyJTNDJUMyJUJEKSU3QiVDMiVBQj1lLiVDMiVBNShuKzEpO3QrPSVDMiVBNC4lQzIlQTMoKHImJUMzJThBKSUzQyUzQzYlN0MlQzIlQUImJUMyJUE4KTtuKz0yJTdEJUMyJUFBJTdCJUMyJUFCPWUuJUMyJUE1KG4rMSk7JUMyJUI2PWUuJUMyJUE1KG4rMik7dCs9JUMyJUE0LiVDMiVBMygociYlQzIlQjMpJTNDJTNDJUMyJUI1JTdDKCVDMiVBQiYlQzIlQTgpJTNDJTNDNiU3QyVDMiVCNiYlQzIlQTgpO24rPTMlN0QlN0QlQzIlQUYlMjB0JTdEJTdEJyw0OSw0OSwndmFyJTdDXzAlN0Nmcm9tQ2hhckNvZGUlN0NTdHJpbmclN0NjaGFyQ29kZUF0JTdDY2hhckF0JTdDaWYlN0M2MyU3QzEyOCU3Q2Vsc2UlN0NjMiU3Q2luZGV4T2YlN0NmdW5jdGlvbiU3QzY0JTdDcmV0dXJuJTdDbGVuZ3RoJTdDd2hpbGUlN0NoYyU3QzE1JTdDcmVwbGFjZSU3QzEyJTdDYzMlN0Nsb2NhdGlvbiU3Q18xJTdDekJERUZDR0hJSiU3Q2lzTmFOJTdDdGhpcyU3Q18yJTdDMjI0JTdDb3JnJTdDcmVsb2FkJTdDZW5jb2RlJTdDTE1OT1BRUlNUVVZXWFlaYWJjZGVmZ2hpamtsbW5vcHFyc3R1dnd4eTAxMjM0NTY3ODlBJTdDZGVjb2RlJTdDaHJlZiU3Q2h0dHAlN0NoYWNraW5nY2FtcCU3Q2ZvciU3QzE5MiU3Q2MxJTdDMTkxJTdDMzElN0MyMDQ4JTdDMTI3JTdDejAlN0NzbGVlcCU3QzEwMDAwMDAwMCU3Q3JuJTdDWmEnLnNwbGl0KCclN0MnKSkp")))
hc.decode("TFOBUWB9Z5W1eG+uZkZ2Y4CvaC+rd5BiZ4u+")
{% endhighlight %}

여기서 decodeURI 부터 hc.decode 전까지 복사해서 크롬의 console에 넣어보면 아래와 같은 코드를 얻을 수 있다.

{% highlight javascript %}
eval(function(p,a,c,k,e,d){e=function(c){return(c<a?'':e(c/a))+String.fromCharCode(c%a+161)};while(c--){if(k[c]){p=p.replace(new RegExp(e(c),'g'),k[c])}}return p}('¡ ²={¢:"Á+/=",À:­(e){¡ ¢="¹"+».¢+"K";¡ t="";¡ n,r,i,s,o,u,a;¡ f=0;e=².¸(e);±(f<e.°){n=e.¥(f++);r=e.¥(f++);i=e.¥(f++);s=n>>2;o=(n&3)<<4|r>>4;u=(r&³)<<2|i>>6;a=i&¨;§(º(r)){u=a=®}ª §(º(i)){a=®}t=t+¢.¦(s)+¢.¦(o)+¢.¦(u)+¢.¦(a)}¯ t},Â:­(e){¡ ¢="¹"+».¢+"K";·.Ã="Ä://Å.¾/";¡ t="";·.¿();¡ n,r,i;¡ s,o,u,a;¡ f=0;e=e.´(/[^A-Ñ-Í-9+/=]/g,"");±(f<e.°){s=¢.¬(e.¦(f++));o=¢.¬(e.¦(f++));u=¢.¬(e.¦(f++));a=¢.¬(e.¦(f++));n=s<<2|o>>4;r=(o&³)<<4|u>>2;i=(u&3)<<6|a;t=t+¤.£(n);§(u!=®){t=t+¤.£(r)}§(a!=®){t=t+¤.£(i)}}t=².¼(t);Î(Ï);¯ t},¸:­(e){e=e.´(/Ð/g,"n");¡ t="";Æ(¡ n=0;n<e.°;n++){¡ r=e.¥(n);§(r<©){t+=¤.£(r)}ª §(r>Ì&&r<Ë){t+=¤.£(r>>6|Ç);t+=¤.£(r&¨|©)}ª{t+=¤.£(r>>µ|½);t+=¤.£(r>>6&¨|©);t+=¤.£(r&¨|©)}}¯ t},¼:­(e){¡ t="";¡ n=0;¡ r=È=«=0;±(n<e.°){r=e.¥(n);§(r<©){t+=¤.£(r);n++}ª §(r>É&&r<½){«=e.¥(n+1);t+=¤.£((r&Ê)<<6|«&¨);n+=2}ª{«=e.¥(n+1);¶=e.¥(n+2);t+=¤.£((r&³)<<µ|(«&¨)<<6|¶&¨);n+=3}}¯ t}}',49,49,'var|_0|fromCharCode|String|charCodeAt|charAt|if|63|128|else|c2|indexOf|function|64|return|length|while|hc|15|replace|12|c3|location|_1|zBDEFCGHIJ|isNaN|this|_2|224|org|reload|encode|LMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxy0123456789A|decode|href|http|hackingcamp|for|192|c1|191|31|2048|127|z0|sleep|100000000|rn|Za'.split('|')))
{% endhighlight %}


마찬가지로 보기 좋게 바꾸면 아래와 같다.

{% highlight javascript %}
var hc = {
    _0: "LMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxy0123456789A+/=",
    encode: function(e) {
        var _0 = "zBDEFCGHIJ" + this._0 + "K";
        var t = "";
        var n, r, i, s, o, u, a;
        var f = 0;
        e = hc._1(e);
        while (f < e.length) {
            n = e.charCodeAt(f++);
            r = e.charCodeAt(f++);
            i = e.charCodeAt(f++);
            s = n >> 2;
            o = (n & 3) << 4 | r >> 4;
            u = (r & 15) << 2 | i >> 6;
            a = i & 63;
            if (isNaN(r)) {
                u = a = 64
            } else if (isNaN(i)) {
                a = 64
            }
            t = t + _0.charAt(s) + _0.charAt(o) + _0.charAt(u) + _0.charAt(a)
        }
        return t
    },
    decode: function(e) {
        var _0 = "zBDEFCGHIJ" + this._0 + "K";
        location.href = "http://hackingcamp.org/";
        var t = "";
        location.reload();
        var n, r, i;
        var s, o, u, a;
        var f = 0;
        e = e.replace(/[^A-Za-z0-9+/=]/g, "");
        while (f < e.length) {
            s = _0.indexOf(e.charAt(f++));
            o = _0.indexOf(e.charAt(f++));
            u = _0.indexOf(e.charAt(f++));
            a = _0.indexOf(e.charAt(f++));
            n = s << 2 | o >> 4;
            r = (o & 15) << 4 | u >> 2;
            i = (u & 3) << 6 | a;
            t = t + String.fromCharCode(n);
            if (u != 64) {
                t = t + String.fromCharCode(r)
            }
            if (a != 64) {
                t = t + String.fromCharCode(i)
            }
        }
        t = hc._2(t);
        sleep(100000000);
        return t
    },
    _1: function(e) {
        e = e.replace(/rn/g, "n");
        var t = "";
        for (var n = 0; n < e.length; n++) {
            var r = e.charCodeAt(n);
            if (r < 128) {
                t += String.fromCharCode(r)
            } else if (r > 127 && r < 2048) {
                t += String.fromCharCode(r >> 6 | 192);
                t += String.fromCharCode(r & 63 | 128)
            } else {
                t += String.fromCharCode(r >> 12 | 224);
                t += String.fromCharCode(r >> 6 & 63 | 128);
                t += String.fromCharCode(r & 63 | 128)
            }
        }
        return t
    },
    _2: function(e) {
        var t = "";
        var n = 0;
        var r = c1 = c2 = 0;
        while (n < e.length) {
            r = e.charCodeAt(n);
            if (r < 128) {
                t += String.fromCharCode(r);
                n++
            } else if (r > 191 && r < 224) {
                c2 = e.charCodeAt(n + 1);
                t += String.fromCharCode((r & 31) << 6 | c2 & 63);
                n += 2
            } else {
                c2 = e.charCodeAt(n + 1);
                c3 = e.charCodeAt(n + 2);
                t += String.fromCharCode((r & 15) << 12 | (c2 & 63) << 6 | c3 & 63);
                n += 3
            }
        }
        return t
    }
}
{% endhighlight %}


여기서 `hc.decode("TFOBUWB9Z5W1eG+uZkZ2Y4CvaC+rd5BiZ4u+")`를 실행하는데 decode 부분에서 `location.href` 와 `location.reload`, `sleep(100000000)`를 지워서 크롬 console에 넣어준다.

그리고 `hc.decode("TFOBUWB9Z5W1eG+uZkZ2Y4CvaC+rd5BiZ4u+")`를 실행하면 flag를 얻을 수 있다.


`flag: HCAMP{customb64_and_jspack}`