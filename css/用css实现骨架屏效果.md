# 骨架屏
### 仿掘金骨架屏
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>
    .item {
      position: relative;
      width: 656px;
      height: 44px;
      margin: 0 auto;
      overflow: hidden;
      background: #f6f6f6;
      color: #fff;
    }
    .fix {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 2;
    }
    svg {
      color: #fff;
    }
    .placeHolder-bg {
      position: absolute;
      top: 0;
      left: 0;
      width: 150%;
      height: 100%;
      background: -webkit-gradient(linear, left top, right top, from(#f6f6f6), color-stop(20%, #ebebeb), color-stop(40%, #f6f6f6), to(#f6f6f6));
      background: linear-gradient(90deg, #f6f6f6 0, #ebebeb 20%, #f6f6f6 40%, #f6f6f6);
      -webkit-animation: linear-double 1s linear 1ms infinite forwards;
      animation: linear-double 1s linear 1ms infinite forwards;
    }

    @keyframes linear-double {
      0% {
        -webkit-transform: translateX(-56%);
        transform: translateX(-56%);
      }
      100% {
        -webkit-transform: translateX(56%);
        transform: translateX(56%);
      }
    }
  </style>
</head>
<body>
<div class='item'>
  <div class='fix'>
    <svg width="656" height="44" viewBox="0 0 656 44" class="PlaceHolder-mask">
      <path d="M0 0h656v44H0V0zm0 0h480v12H0V0zm0 32h238v12H0V32z" fill="currentColor" fill-rule="evenodd"></path>
    </svg>
  </div>
  <div class="placeHolder-bg"/>
</div>
</div>
</body>
</html>

```
