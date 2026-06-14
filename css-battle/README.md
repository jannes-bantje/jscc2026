# CSS Battle

Hosted by @Narigo

We did this battle: https://cssbattle.dev/play/168

```
<div></div>
<style>
  body{margin:0}
  div {
    width: 70px;
    height: 20px;
    background: #DC6638;
    box-shadow:
      0px 0px 0px 0px #DC6638,
      0px 0px 0px 20px #0D1335,
      0px 0px 0px 40px #DC6638,
      0px 0px 0px 60px #0D1335,
      0px 0px 0px 80px #DC6638,
      0px 0px 0px 100px #0D1335,
      0px 0px 0px 120px #DC6638,
      0px 0px 0px 140px #0D1335,
      0px calc(100vh - 20px) 0px 0px #DC6638,
      0px calc(100vh - 20px) 0px 20px #0D1335,
      0px calc(100vh - 20px) 0px 40px #DC6638,
      0px calc(100vh - 20px) 0px 60px #0D1335,
      0px calc(100vh - 20px) 0px 80px #DC6638,
      0px calc(100vh - 20px) 0px 100px #0D1335,
      0px calc(100vh - 20px) 0px 120px #DC6638,
      0px calc(100vh - 20px) 0px 140px #0D1335,
      calc(100vw - 70px) 0px 0px 0px #DC6638,
      calc(100vw - 70px) 0px 0px 20px #0D1335,
      calc(100vw - 70px) 0px 0px 40px #DC6638,
      calc(100vw - 70px) 0px 0px 60px #0D1335,
      calc(100vw - 70px) 0px 0px 80px #DC6638,
      calc(100vw - 70px) 0px 0px 100px #0D1335,
      calc(100vw - 70px) 0px 0px 120px #DC6638,
      calc(100vw - 70px) 0px 0px 140px #0D1335,
      calc(100vw - 70px) calc(100vh - 20px) 0px 0px #DC6638,
      calc(100vw - 70px) calc(100vh - 20px) 0px 20px #0D1335,
      calc(100vw - 70px) calc(100vh - 20px) 0px 40px #DC6638,
      calc(100vw - 70px) calc(100vh - 20px) 0px 60px #0D1335,
      calc(100vw - 70px) calc(100vh - 20px) 0px 80px #DC6638,
      calc(100vw - 70px) calc(100vh - 20px) 0px 100px #0D1335,
      calc(100vw - 70px) calc(100vh - 20px) 0px 120px #DC6638,
      calc(100vw - 70px) calc(100vh - 20px) 0px 140px #0D1335
      ;
  }
</style>
```

Another solution with clipping an hour-glass (🤯):

```
<div a></div>
<div b></div>
<style>
  body{margin:0;}
  div {
    position:fixed;
    width:100%;
    height:100%;
  background:#DC6638;
  }
[a]{
  background:
    linear-gradient(#DC6638 20px,#0D1335 0 40px) 0 0 / 100% 40px;
}
[b] {
  clip-path: polygon(50px 0, 350px 0, 50px 100%, 350px 100%);
  background:
      linear-gradient(90deg,#DC6638 20px,#0D1335 0 40px) 50px 0 / 40px 100%;
  }
```