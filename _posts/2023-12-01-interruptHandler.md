---
layout: single
title:  "12/1 OS 용어"
categories: [Programming, python, OS]
tag: [Programming, python, OS]
toc: true
author_profile: false
sidebar:
    nav: "docs"
---

* interrupt handler : 인터럽트에 대응하여 특정 기능을 처리하는 기계어 루틴이다. 인터럽트는 예외 상황이나 하드웨어에서의 이벤트가 발생했을 때 현재 실행중인 프로그램의 흐름을 중단하고, 미리 정의된 인터럽트 핸들러로 제어를 전달하여 특정 작업을 수행한다. 이를 통해 컴퓨터 시스템은 예외 상황이나 하드웨어 이벤트에 대응할 수 있다..

   

* page table : 가상 메모리를 물리 메모리에 매핑하는 데 사용되는 자료구조이다. 프로세스가 가상 주소를 사용하면, 이 주소는 페이지로 나뉘어지고 각 페이지는 물리 메모리의 프레임에 매핑된다. 페이지 테이블은 이러한 가상 주소와 물리 주소 간의 매핑 정보를 가지고 있다.

     가상 메모리는 프로세스를 필요로 하는 코드의 일부분만 물리 메모리에 적재함으로써 메모리 공간을 효율적으로 사용할 수 있도록 도와준다. 페이지 테이블은 이러한 가상 메모리의 페이지들을 물리 메모리의 프레임에 매핑하는데 사용되며, 이 매핑정보를 참조하여 가상 주소를 물리 주소로 변환한다.

    페이지 테이블은 보통 하드웨어의 지원을 받아 관리되며, 가상 주소 변환을 위한 효율적인 메커니즘을 제공한다. 페이지 테이블의 업데이트는 운영체제가 담당하며, 페이지 폴트와 같은 상황에서 새로운 페이지를 적재하고 매핑 정보를 갱신한다. 페이지 테이블을 통해 메모리의 가상화와 보호 기능을 구현할 수 있다.

  

* page fault : 메모리 관리 시스템에서 발생하는 현상으로, 프로그램이나 프로세스가 요청한 페이자가 현제 물리 메모리에 없는 경우를 나타낸다. 페이지 폴트는 가상 메모리 시스템에서 주로 발생하며 페이지 폴트가 발생하면 운영체제는 요청한 페이지를 물리 메모리에 적재하는 과정을 거친다. 

  페이지 폴트는 다양한 메모리 관리 메커니즘에서 중요한 역할을 한다. 페이지 폴트가 발생하면 성능 저하가 발생할 수 있기 때문에, 효율적인 페이지 교체 알고리즘이나 페이지 프레임 할당 기법이 사용되어야 한다.
