# Hi6 로봇제어기 기능설명서 - SoftXYZ 기능


본 제품 설명서에서 제공되는 정보는 현대로보틱스의 자산입니다.

현대로보틱스의 서면에 의한 동의 없이 전부 또는 일부를 무단 전재 및 재배포할 수 없으며, 제3자에게 제공되거나 다른 목적에 사용할 수 없습니다.



본 설명서는 사전 예고 없이 변경될 수 있습니다.

<br>
<br>
<br>
<br>

**Copyright ⓒ 2025 by HD Hyundai Robotics**
# 🧩 1. 개요

SoftXYZ 기능은 **센서리스 힘제어** 방식으로, 사용자가 설정한 환경에서 로봇이 외력에 대해 **직교 좌표** 기준으로 유연하게 움직이도록 하는 기능입니다.

기능을 정확하게 사용하기 위해서는 로봇에 장착된 **툴(Tool)** 또는 **부가 중량(Payload)** 정보를 정확히 설정해야 합니다.  

본 기능은 **소프트웨어 기반**으로 동작하므로, **힘/토크 센서 등 별도의 추가 하드웨어 없이** 사용할 수 있습니다.



--- 

## ⚠️ 주의사항

softxyz 기능은 **힘 센서를 사용하지 않는 기반 기능**이기 때문에,  
부드럽고 자연스러운 모션 구현에는 **물리적 한계가 존재**합니다.

다만, `softxyz_lim` 설정값을 작업 환경에 맞게 적절히 조절하면  
최대한 부드러운 모션을 구현할 수 있습니다.

`softxyz_lim (pos / xnr / vel / thr)` 값은 로봇이 외력에 반응하는 정도를 직접적으로 결정하므로,   환경 · 조립 공정 · 툴 강성 등에 따라 **세밀한 튜닝이 필요**합니다.

# 🧩 2. 명령어

SoftXYZ 기능은 두 개의 명령어(`softxyz_lim`, `softxyz`)를 통해 설정 및 제어됩니다.

- **`softxyz_lim`** 명령어는 SoftXYZ 동작에 필요한 **기본 제한 파라미터를 사전에 정의**하는 역할을 합니다.
- **`softxyz`** 명령어는 `softxyz_lim`에서 설정된 파라미터를 기준으로 **SoftXYZ 기능을 활성화 또는 비활성화**합니다.

따라서 SoftXYZ 기능을 사용하기 위해서는, 반드시 `softxyz_lim` 명령어를 먼저 사용하여 **좌표축별 제한 조건과 동작 범위**를 설정한 후 `softxyz on` 명령어를 통해 기능을 활성화해야 합니다.

## 🧩 2.1 softxyz

센서를 사용하지 않고 외력에 대해 직교좌표 기준으로 로봇이 밀리는 기능

<br>

### 문법

```python
softxyz on, crd=<기준좌표계>
softxyz set, dpr=<강성>
softxyz off
```
---

### 파라미터 
- **on** : softxyz 기능 시작  
- **off** : softxyz 기능 종료  
- **set** : softxyz 설정값 변경  

- **crd** : 외력 반응 기준 좌표계  
  - 사용 가능: `base`, `robot`, `tool`, `user_x`

- **dpr** : 강성(Stiffness) 값  
  - 범위: **0.0 ~ 2.0**  
  - 값이 **클수록 단단해져 외력에 덜 밀림**  
  - 기본값: **1.0**
```python
softxyz on,  crd="base"     # 베이스 좌표계 기준
softxyz on,  crd="robot"    # 로봇 좌표계 기준
softxyz on,  crd="tool"     # 툴 좌표계 기준
softxyz on,  crd="user_1"   # 사용자 정의 좌표계 1번

softxyz set, dpr=1.0        # 강성값 설정 (0.0~2.0, 값이 클수록 단단함)
softxyz off                  # 기능 종료 
```

<br>

> ✅ **정보**  
> - `softxyz on`을 사용하기 전에 **반드시** `softxyz_lim` 명령을 통해  
>   `pos`, `xnr`, `vel`, `thr` 값을 설정해야 합니다.  
>   (최대 밀림 거리, 속도, 직교좌표 문턱값 설정 필수)
>
> - 외력 민감도를 높이기 위해 `softxyz on` 실행 전에  
>   **delay 명령으로 1~2초 동안 로봇을 정지**시켜 두는 것이 좋습니다.
>
> - softxyz 동작 중 떨림이 발생할 경우 다음과 같은 조치를 권장합니다.
>   1) *thr 값을 높인다*  
>   2) *dpr 값을 높인다*  
>   3) *vel 값을 낮춘다*
## 🧩 2.2 softxyz_lim 

softxyz_lim 명령어는 softxyz on 기능 사용 전 파라미터 값을 미리 설정 해야 합니다. <br>

사용자는 활성화 되는 로봇의 직교좌표 거리 제한, 위치, 속도 및 문턱값등을 설정 할 수 있습니다.    

<br>


### 설명 
* softxyz 파라미터를 설정합니다.  


### 문법 
```pythonghlt
softxyz_lim pos,_x=<+X거리>,x_=<-X거리>,_y=<+Y거리>,y_=<-Y거리>,_z=<+Z거리>,z_=<-Z거리> 
softxyz_lim vel,x=<X속도>,y=<Y속도>,z=<Z속도>,rx=<Rx속도>,ry=<Ry속도>,rz=<Rz속도> 
softxyz_lim xnr,x=<X거리>,y=<Y거리>,z=<Z거리>,rx=<Rx거리>,ry=<Ry거리>,rz=<Rz거리> 
softxyz_lim thr,x=<X문턱값>,y=<Y문턱값>,z=<Z문턱값>,rx=<Rx문턱값>,ry=<Ry문턱값>,rz=<Rz문턱값> 
```

### 파라미터 
* softxyz_lim pos : 로봇이 이동할 수 있는 직교좌표 최대 거리를 설정합니다. (X,Y,Z방향) [mm] 
* softxyz_lim vel : 로봇이 동작하는 직교좌표 최대 속도를 설정합니다. (X,Y,Z,Rx,Ry,Rz방향) [mm/sec] or [deg/sec] 
* softxyz_lim xnr : 로봇이 이동할 수 있는 직교좌표 최대 거리와 각도를 제한합니다. (X,Y,Z,Rx,Ry,Rz방향) [mm] or [deg] <br> 
  (로봇의 최대 동작영역은 pos와 xnr의 합집합으로 결정됩니다.)  
* softxyz_lim thr : 로봇이 이동하기 위한 직교좌표 힘 문턱값을 설정합니다. (X,Y,Z,Rx,Ry,Rz방향) [N] or [Nm]


### 사용 예 
> * +X방향200[mm], -Y방향100[mm], +Z방향300[mm]로 이동하는 최대 거리를 설정합니다.  
```python
softxyz_lim pos, _x=200, y_=100, _z=300
```
> * Z방향 최대 이동 속도를 40mm/sec로 설정합니다. 
```python
softxyz_lim vel, z=40
```
> * X방향으로 이동하는 최대 거리를 -200[mm]에서 200[mm]로 설정합니다. 
```python
softxyz_lim xnr, x=200
```
> * 직교좌표 Y방향의 힘 문턱값을 10[N]으로 설정합니다. 
```python
softxyz_lim thr, y=10
```
# 🧩 3. 예시

본 절에서는 SoftXYZ 기능의 실제 사용 방법을 이해할 수 있도록  
`softxyz_lim` 및 `softxyz` 명령어를 활용한 **대표적인 설정 및 프로그램 예제**를 제공합니다.

각 예제는 적용 좌표축, 이동 범위, 속도 제한, 문턱값 등 주요 파라미터 설정에 따른 SoftXYZ 동작 특성을 확인하는 것을 목적으로 하며,  
실제 접촉 작업 및 힘 제어 환경에서의 응용을 고려하여 구성되었습니다.
## 🧩 3.1 예제 

* Z방향으로 조립하기 위해 X, Y, Ry 방향으로 밀릴 수 있도록 한 경우  

<br> 

> * 좌표계 : 로봇좌표계 기준 (crd="robot") <br>
> * 이동 위치(xnr) 제한 설정 : X, Y방향으로 [-50,+50] 범위(mm), Ry방향 [-3,+3] 범위(deg) <br>
> * 속도(vel) 제한 설정 : X, Y방향으로 최대 5mm/sec, Ry방향으로 3deg/sec 밀리도록 설정 <br>
> * 문턱값(thr) 제한 설정 : X방향 3N, Y방향 3N 그리고 Ry방향 1Nm 

```python
S1   move P,spd=100mm/sec,accu=0,tool=0
     delay 2.0 # softxyz on 하기 전에 delay 설정 필수  
     softxyz_lim xnr, x=50, y=50, ry=3
     softxyz_lim vel, x=5, y=5, ry=3
     softxyz_lim thr, x=20, y=20, ry=3
     softxyz on, crd="robot"
S2   move P,spd=250mm/sec,accu=0,tool=0
     softxyz off 
     end 
```

## 🧩 3.2 예제

* 사출물 핸들링

> * 좌표계 : 로봇좌표계 기준 (crd="robot") <br>
> * 위치(pos) 제한 설정 : +Y방향으로 최대 300mm까지, -Y방향으로 최대 200mm 까지 이동 <br>
> * 속도(vel) 제한 설정 : Y방향으로 최대 150mm/sec 속도로 밀리도록 설정 <br>

```python
S1   move P,spd=100mm/sec,accu=0,tool=0
     delay 2.0 # softxyz on 하기 전에 delay 설정 필수  
     softxyz_lim pos, _y=300, y_=200
     softxyz_lim vel, y=150
     softxyz on, crd="robot"
S2   wait ... 
     softxyz off 
     end 
```
