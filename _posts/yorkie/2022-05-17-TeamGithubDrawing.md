---
title:  "yorkie-team.github.io drawing 분석"

categories: 
  - yorkie
tags: 
  - [Open Source, yorkie, yorkie-team.github.io]

toc: true
toc_sticky: true

date: 2022-05-17
last_modified_at: 2022-05-17
---
# drawing

### main()

```jsx
async function main() {
    try {
				// yorkie client 생성 및 활성화
        const client = yorkie.createClient('http://localhost:8080');
        await client.activate();

				// document 생성 및 클라이언트에 attach
        const doc = yorkie.createDocument(`example-${getYYYYMMDD()}`);
        await client.attach(doc);
				
        await createDrawingExample(client, doc, drawingPanel);
    } catch (e) {
        console.error(e);
    }
}
```

### createDrawingExample(client, doc, drawingPanel)

```jsx
async function createDrawingExample(client, doc, drawingPanel) {
		// root.shapes : local+remote에서 해당 document에 대해 벌어진 모든 변경사항을 저장
    doc.update((root) => {
        if (!root.shapes) {
            root.shapes = [];
        }
    }, 'create points if not exists');

		// subscribe : DocEvent가 발생하면 paintCanvas를 실행
    doc.subscribe((event) => {
        paintCanvas(drawingPanel, doc.getRoot().shapes);
    });
    await client.sync();

		// 로컬의 변경사항에 대한 handler 정의
    const handlers = {
				// 그리기 시작하는 상황에 대한 handler
        'begin': (e) => {
            const point = getPoint(drawingPanel, e);
            if (point.x < 0 || point.y < 0 ||
                point.x > drawingPanel.offsetWidth || point.y > drawingPanel.offsetHeight) {
                return;
            }

            window.isStartDragging = true;
						// root.shapes에 로컬의 변경사항을 push 
            doc.update((root) => {
                root.shapes.push({
                    points: [point] // 각 점의 정보를 담을 shape 배열
                });
								// window.currentID에 해당 root.shape의 식별자를 저장
                window.currentID = root.shapes.getLast().getID();
            }, `update content by ${client.getID()}`);
        },

				// 그리는 동작(클릭한 상태에서 커서를 움직이는)에 대한 handler
        'move': (e) => {
            if (!window.isStartDragging) {
                return;
            }

            const point = getPoint(drawingPanel, e);
            if (point.x < 0 || point.y < 0 ||
                point.x > drawingPanel.offsetWidth || point.y > drawingPanel.offsetHeight) {
                e.preventDefault();
                return;
            }

            doc.update((root) => {
								// window.currentID 식별자를 통해 해당하는 root.shape 배열을 가져옴
                const shape = root.shapes.getElementByID(window.currentID);
								// shape에 변경사항이 일어난 점을 저장
                shape.points.push(point);
								// canvas에 변경사항들을 반영
                paintCanvas(drawingPanel, root.shapes);
            }, `update content by ${client.getID()}`);
        },

				// 그리는 동작의 종료에 대한 handler
        'end': (e) => {
            if (window.isStartDragging) {
                window.isStartDragging = false;
            }
        },
    };

    // for desktop
    document.addEventListener('mousedown', handlers['begin']);
    document.addEventListener('mousemove', handlers['move']);
    document.addEventListener('mouseup', handlers['end']);

    // for touch devices
    document.addEventListener('touchstart', handlers['begin']);
    document.addEventListener('touchmove', handlers['move']);
    document.addEventListener('touchend', handlers['end']);

    // 05. set initial value.
    paintCanvas(drawingPanel, doc.getRoot().shapes);
}
```

### paintCanvas(drawingPanel, shapes)

```tsx
function paintCanvas(drawingPanel, shapes) {
  // TODO Now repainting the whole thing. Only changed parts should be drawn.
  const context = drawingPanel.getContext('2d');
  context.clearRect(0, 0, drawingPanel.offsetWidth, drawingPanel.offsetHeight);

  for (const shape of shapes) {  // shapes 안의 변경 내역 배열인 shape를 순회
    context.beginPath();
    let isMoved = false;
    for (const p of shape.points) {  // shape안의 point들을 순회
      if (isMoved === false) {
        isMoved = true;
        context.moveTo(p.x, p.y);  // context에 점을 반영
      } else {
        context.lineTo(p.x, p.y);  // context에 선을 반영
      }
    }

    context.stroke();
  }
}
```