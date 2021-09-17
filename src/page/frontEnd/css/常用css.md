## css赛贝尔曲线动效（回弹效果）
transition: transform 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);

### 水墨效果
https://codepen.io/alphardex/pen/BaBevXm

```
&::after {
      position: absolute;
      content: "";
      left: 0;
      width: inherit;
      height: inherit;
      background: white;
      transition: 30s cubic-bezier(0.19, 1, 0.22, 1);
    }

    &:hover::after {
      transform: scale(0);
      transition-duration: 0.3s;
    }
```