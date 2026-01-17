# 2. 명령어

SoftXYZ 기능은 두 개의 명령어(**softxyz_lim**, **softxyz**)를 통해 설정 및 제어됩니다.

- ****softxyz_lim**** 명령어는 SoftXYZ 동작에 필요한 **기본 제한 파라미터를 사전에 정의**하는 역할을 합니다.
- **softxyz** 명령어는 **softxyz_lim**에서 설정된 파라미터를 기준으로 **SoftXYZ 기능을 활성화 또는 비활성화**합니다.

따라서 SoftXYZ 기능을 사용하기 위해서는, 반드시 **softxyz_lim** 명령어를 먼저 사용하여 **좌표축별 제한 조건과 동작 범위**를 설정한 후 **softxyz on** 명령어를 통해 기능을 활성화해야 합니다.

