﻿---
title: "SOCAMM"
date: 2025-06-25T22:33:16+09:00
lastmod: 2025-06-25T22:33:16+09:00
type: docs
sidebar:
  open: true
weight: 5
---
<div style="display:none">
  <meta property="article:published_time" content="2025-06-25T13:33:16Z" />
  <meta property="article:modified_time" content="2025-06-25T13:33:16Z" />
</div>
SOCAMM(System On Chip with Advanced Memory Module)!Pasted image 20250625133311.png

온디바이스 AI를 위한 차세대 메모리 SOCAMM

- 최근 AI 산업 내에서 SOCAMM(System On Chip with Advanced Memory Module)에 대한 관심이 높아지고 있음
  
- 특히 NVIDIA의 개인용 슈퍼컴퓨터인 디지츠(Digits)가 지난 1월 CES에서 공개되며 향후 차세대 AI 디바이스에서 새로운 종류의 메모리 모듈이 사용될 가능성이 활발히 논의되고 있음
  
- 현재 많은 주목을 받고 있는 차세대 메모리 모듈은 SOCAMM이지만 이외에도 LLW(Low Latency Wide I/O), LPCAMM(Low Power Compression Attached Memory Module) 등 다양한 종류의 메모리 모듈이 AI 디바이스용 메모리 모듈로 논의되고 있음
  
- 먼저 SOCAMM은 현재 스마트폰이나 노트북에 많이 탑재되고 있는 SoC(System on Chip)와 메모리를 단일 패키지에 함께 실장한 모듈로 AI 디바이스에서 고대역폭, 저전력, 폼팩터 소형화에 대한 필요성이 대두되기 때문에 각광받고 있음
  
- 먼저 AI 연산을 위해서는 고대역폭의 메모리가 필요한데 기존 DIMM 방식은 SoC와 메모리 간 거리가 멀기 때문에 대역폭이 낮으며 Latency가 발생함
  
- 반면 SOCAMM은 SoC와 메모리의 물리적 거리가 가깝기 때문에 로직과 메모리간 통신 효율을 개선할 수있으며 이로 인해 고대역폭, 저지연 특성을 구현할 수 있음
  
- 또한 배터리를 기반으로 작동하는 AI 디바이스의 경우 AI 연산을 처리하는 과정에서 디바이스의 배터리 소모가 심해질 수 있기에 저전력 특성이 요구되는데 기존 방식(DIMM, MCP 등)을 이용할 경우에 SoC와 메모리가 PCB(Printed Circuit Board)를 통해 연결되어 있어 [SoC – 메모리 컨트롤러– PCB 트레이스 – 메모리 모듈]의 복잡한 통신 과정을 거쳐 소통을 해야 함
  
- 위와 같이 긴 거리를 거쳐 소통해야 할 경우 높은 전압이 요구되고 이로 인해 배터리 소모가 심해지는 부정적 영향이 있음
  
- 반면 SOCAMM의 경우 SoC와 메모리가 SoC 내부의 메모리 컨트롤러를 통해 직접 소통하기 때문에 낮은 전압으로도 통신이 가능하며 결과적으로 배터리 소모가 적어짐
  
- 마지막으로 기존 방식을 사용할 경우 PCB 기판에서 메모리와 SoC 사이를 연결하는 배선 공간이 필요하기 떄문에 폼팩터가 불필요하게 커짐
  
- 반면 단일 패키지 내부에 SoC와 메모리를 함께 실장하면 PCB 설계가 단순화되기 때문에 폼팩터 소형화가 가능해짐
  
- 현재 SOCAMM이 본격적으로 채용되고 있는 상황은 아니지만 향후 채용을 위한 초반 작업이 시작된 것으로 보임
  
- 향후 AI 산업이 빠른 속도로 발전하고 AI 디바이스가 일상으로 침투하며 SOCAMM, LPCAMM, LLW 등이 차세대 메모리로서의 역할을 할 것으로 기대됨
