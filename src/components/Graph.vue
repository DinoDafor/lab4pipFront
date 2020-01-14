<template>
    <div class="graph">
        <!--wi he сделать входными, через : -->
        <canvas class="canvas"
                ref="canvas"
                v-bind:width="$props.width"
                v-bind:height="$props.height"
        />


    </div>
</template>

<script>


    export default {
        name: "Graph",
        components: {},
        props: {
            dots: Array,
            width: Number,
            height: Number,
            value: Number,
            r: Number,
            prevResults: Number,
        },
        watch: {
            r() {
                if (this.$props.r <= 0) {
//todo  сделать мем
                } else {
                    this.draw();
                    this.toDrawCirclesOnCanvas();
                }

            },

        },
        data() {
            return {
                canvasContext: null,
                // mouseDownFlag: false,
                // rSmooth: 0,
                // rUpdatingIntervalId: null,
            }
        },
        // watch: {
        //     r() {
        //         this.render();
        //     },
        //     rSmooth() {
        //         this.render();
        //     },
        //     'value.x': {
        //         handler: function () {
        //             this.render();
        //         },
        //         deep: true
        //     },
        //     'value.y': {
        //         handler: function () {
        //             this.render();
        //         },
        //         deep: true
        //     },
        //     prevResults() {
        //         this.render();
        //     }
        // },
        mounted() {
            this.canvasContext = this.$refs.canvas.getContext('2d');
            // //Более гибкая функция moveTo
            // this.canvasContext.mv = function (x, y) {
            //     this.moveTo(x * this.width, y * this.height);
            // };
            // //Более гибкая функция lineTo
            // this.canvasContext.ln = function (x, y) {
            //     this.lineTo(x * this.width, y * this.height);
            // };
            // //Более гибкая функция fillText
            // this.canvasContext.txt = function (text, x, y) {
            //     this.fillText(text, x * this.width, y * this.height);
            // };
            //todo временно, это входные данные
            //this.$props.width = 250;
            // this.$props.height = 250;
            //this.$props.r = 100; //это должно быть пиксельно, раньше было 50 px
            //временно
            this.draw();

            //this.toDrawCirclesOnCanvas();
            //this.draw();
            //Рисуем график
            // this.render();
            //
            // this.rUpdatingIntervalId = setInterval(this.updateR, 1000 / 60);
        },
        methods: {//Тестовые методы для отрисовки
            checkDot(x, y, r) {
                if (x <= 0 && y <= 0 && x >= -r && y >= -r / 2) return true;
                if (x <= 0 && y >= 0 && y <= x * 2 + r) return true;
                if (x >= 0 && y <= 0 && x * x + y * y < r * r / 4) return true;
                return false;
            },
            toDrawCirclesOnCanvas() {
                let ctx = this.$refs.canvas.getContext('2d');
                for (let i = 0; i < this.dots.length; i++) {

                    let x = this.dots[i].newX;
                    let y = this.dots[i].newY;

                    let pixelX = x * 100 / 2.0;
                    let pixelY = y * 100 / 2.0;
                    //let rFromServer = this.dots[i].newR;
                    //   let result = this.dots[i].newInArea;

                    let resultForCanvas = this.checkDot(x, y, this.$props.r);

//функция, которая принимает радиус, который мы выбрали и возвращает бул result
                    pixelX = pixelX + 125;
                    pixelY = pixelY - 125;
                    pixelY = -pixelY;

                    // alert(pixelX);
                    // alert(pixelY);
                    // alert(rFromServer);
                    // alert(result);


                    // let pixelX = x * (100 / 3.0);
                    // let  pixelY = (y * (100 / 3.0));
                    //
                    // pixelX = (pixelX / oldRadius) * newRadius + 125;
                    // pixelY = (pixelY / oldRadius) * newRadius - 125;
                    // pixelY = -pixelY;

                    ctx.beginPath();
                    //todo оптимизировать
                    if (resultForCanvas) {

                        ctx.strokeStyle = "green";
                        ctx.fillStyle = "green";

                    } else {
                        ctx.strokeStyle = "red";
                        ctx.fillStyle = "red";
                    }
                    //todo изменить радиус кружка
                    ctx.arc(pixelX, pixelY, 2.5, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.stroke();
                    ctx.closePath();
                }

                // let result = true;
                //
                //
                //
                // let pixelX = x * (100 / 3.0);
                // let  pixelY = (y * (100 / 3.0));
                //
                // pixelX = (pixelX / oldRadius) * newRadius + 125;
                // pixelY = (pixelY / oldRadius) * newRadius - 125;
                // pixelY = -pixelY;
                //
                //
                //
                // ctx.beginPath();
                // //todo оптимизировать
                // if (result) {
                //
                //     ctx.strokeStyle = "green";
                //     ctx.fillStyle = "green";
                //
                // } else {
                //     ctx.strokeStyle = "red";
                //     ctx.fillStyle = "red";
                // }
                // //todo изменить радиус кружка
                // ctx.arc(pixelX, pixelY, 2.5, 0, Math.PI * 2);
                // ctx.fill();
                // ctx.stroke();
            },
            draw() {
                let ctx = this.canvasContext;
                //todo  временно, эти данные извне поступают
                let width = this.$props.width;
                let height = this.$props.height;
                let radius = this.$props.r * 50;
                // временно
                ctx.clearRect(0, 0, width, height); //Очищаем график, через квадрат
                //делаем обводку
                ctx.strokeRect(0, 0, width, height);

                //я хз пока как, но допустим полотно квадратное todo переделать
                let centre = width / 2;
                let minusRadOfX = centre - radius;
                let minusRadDivTwoOfX = centre - (radius / 2);
                let plusRadDivTwoOfX = centre + (radius / 2);
                let plusRadOfX = centre + radius;
                //ПОМНИМ, ЧТО Y РАСТЁТ ВНИЗ!
                let plusRadOfY = centre - radius;
                let plusRadDivTwoOfY = centre - (radius / 2);
                let minusRadDivTwoOfY = centre + (radius / 2);
                let minusRadOfY = centre + radius;

                //todo переделать, что это?))
                let marking = width / 50;


                ctx.beginPath();
                //тут раньше было colorSystem, lineWidth, передавалось извне, надо теперь подумать, как тоже передавать
                // ctx.fillStyle = 'forestgreen';
                // ctx.strokeStyle = 'forestgreen';
                // ctx.lineWidth = 2;

                //todo временные фигуры
                ctx.strokeStyle = 'forestgreen';
                ctx.fillStyle = 'purple';
                ctx.lineWidth = 1;

                //1) Рисуем фигуры
                //1.1)Прямоугольник в третьей четверти
                ctx.rect(centre, centre, -radius, radius / 2);
                //1.2)Арка в четвёртой четверти
                ctx.arc(centre, centre, radius / 2, 0, Math.PI / 2, false);
                //1.2)Арка в первой четверти
                // context.arc(centre, centre, radius, 0, -Math.PI / 2, true);
                //1.3)Треугольник во второй четверти
                ctx.moveTo(minusRadDivTwoOfX, centre);
                ctx.lineTo(centre, plusRadOfY);
                ctx.lineTo(centre, centre);
                ctx.moveTo(centre, centre);
                ctx.fill(); //окрашиваем
                ctx.stroke(); //выводим
                ctx.closePath();

                ctx.beginPath();

                ctx.strokeStyle = 'forestgreen';
                ctx.fillStyle = 'forestgreen';
                ctx.lineWidth = 2;

                //Стрелка по Y вниз
                ctx.moveTo(centre, centre);
                ctx.lineTo(centre, height);
                //по Y первая нижняя чёрточка, -R/2
                ctx.moveTo(centre, minusRadDivTwoOfY);
                ctx.lineTo(centre + marking, minusRadDivTwoOfY);
                ctx.moveTo(centre, minusRadDivTwoOfY);
                ctx.lineTo(centre - marking, minusRadDivTwoOfY);
                //по Y вторая нижняя чёрточка, -R
                ctx.moveTo(centre, minusRadOfY);
                ctx.lineTo(centre - marking, minusRadOfY);
                ctx.moveTo(centre, minusRadOfY);
                ctx.lineTo(centre + marking, minusRadOfY);
                //Стрелка по Y вверх
                ctx.moveTo(centre, centre);
                ctx.lineTo(centre, 0);
                //по Y первая верхняя чёрточка, R/2
                ctx.moveTo(centre, plusRadDivTwoOfY);
                ctx.lineTo(centre + marking, plusRadDivTwoOfY);
                ctx.moveTo(centre, plusRadDivTwoOfY);
                ctx.lineTo(centre - marking, plusRadDivTwoOfY);
                //по Y вторая верхняя чёрточка, R
                ctx.moveTo(centre, plusRadOfY);
                ctx.lineTo(centre + marking, plusRadOfY);
                ctx.moveTo(centre, plusRadOfY);
                ctx.lineTo(centre - marking, plusRadOfY);
                //левый "усик" по Y
                ctx.moveTo(centre, 0);
                ctx.lineTo(centre - (marking * 3), marking * 3);
                //правый "усик" по Y
                ctx.moveTo(centre, 0);
                ctx.lineTo(centre + (marking * 3), marking * 3);
                //Стрелка по Х влево
                ctx.moveTo(centre, centre);
                ctx.lineTo(0, centre);
                //по Х первая левая чёрточка, -R/2
                ctx.moveTo(minusRadDivTwoOfX, centre);
                ctx.lineTo(minusRadDivTwoOfX, centre + marking);
                ctx.moveTo(minusRadDivTwoOfX, centre);
                ctx.lineTo(minusRadDivTwoOfX, centre - marking);
                //по Х вторая левая чёрточка, -R
                ctx.moveTo(minusRadOfX, centre);
                ctx.lineTo(minusRadOfX, centre + marking);
                ctx.moveTo(minusRadOfX, centre);
                ctx.lineTo(minusRadOfX, centre - marking);
                //Стрелка по Х вправо
                ctx.moveTo(centre, centre);
                ctx.lineTo(width, centre);
                //по Х первая правая чёрточка, R/2
                ctx.moveTo(plusRadDivTwoOfX, centre);
                ctx.lineTo(plusRadDivTwoOfX, centre + marking);
                ctx.moveTo(plusRadDivTwoOfX, centre);
                ctx.lineTo(plusRadDivTwoOfX, centre - marking);
                //по Х вторая правая чёрточка, R
                ctx.moveTo(plusRadOfX, centre);
                ctx.lineTo(plusRadOfX, centre + marking);
                ctx.moveTo(plusRadOfX, centre);
                ctx.lineTo(plusRadOfX, centre - marking);
                //нижний "усик" по Х
                ctx.moveTo(width, centre);
                ctx.lineTo(width - (marking * 3), centre + (marking * 3));
                //верхний "усик" по Х
                ctx.moveTo(width, centre);
                ctx.lineTo(width - (marking * 3), centre - (marking * 3));

                //3) рисуем поверх фигур радиус

                //По Y
                ctx.fillText("-R/2", centre + (2 * marking), minusRadDivTwoOfY);
                ctx.fillText("-R", centre + (2 * marking), minusRadOfY);
                ctx.fillText("R/2", centre + (2 * marking), plusRadDivTwoOfY);
                ctx.fillText("R", centre + (2 * marking), plusRadOfY);
                //По X
                ctx.fillText("-R/2", minusRadDivTwoOfX, centre - marking);
                ctx.fillText("-R", minusRadOfX, centre - marking);
                ctx.fillText("R/2", plusRadDivTwoOfX, centre - marking);
                ctx.fillText("R", plusRadOfX, centre - marking);
                ctx.stroke();
                ctx.closePath();


            },
            draw1() {
                let ctx = this.canvasContext;
                //todo  временно, эти данные извне поступают
                let width = this.$props.width;
                let height = this.$props.height;
                let radius = this.$props.r;
//todo возможно убрать
                ctx.clearRect(0, 0, width, height);

                //я хз пока как, но допустим полотно квадратное todo переделать
                let centre = width / 2;
                //  let minusRadOfX = centre - radius;
                let minusRadDivTwoOfX = centre - (radius / 2);
                //   let plusRadDivTwoOfX = centre + (radius / 2);
                //  let plusRadOfX = centre + radius;
                //ПОМНИМ, ЧТО Y РАСТЁТ ВНИЗ!
                let plusRadOfY = centre - radius;
                //  let plusRadDivTwoOfY = centre - (radius / 2);
                //   let minusRadDivTwoOfY = centre + (radius / 2);
                //  let minusRadOfY = centre + radius;
                //делаем обводку
                ctx.strokeRect(0, 0, width, height);
                //
                ctx.beginPath();
                //todo тут раньше было colorOfShapes, lineWidth, передавалось извне, надо теперь подумать, как тоже передавать
                ctx.strokeStyle = 'purple';
                ctx.fillStyle = 'purple';
                ctx.lineWidth = 1;

                //1) Рисуем фигуры
                //1.1)Прямоугольник в третьей четверти
                ctx.rect(centre, centre, -radius, radius / 2);
                //1.2)Арка в четвёртой четверти
                ctx.arc(centre, centre, radius / 2, 0, Math.PI / 2, false);
                //1.2)Арка в первой четверти
                // context.arc(centre, centre, radius, 0, -Math.PI / 2, true);
                //1.3)Треугольник во второй четверти
                ctx.moveTo(minusRadDivTwoOfX, centre);
                ctx.lineTo(centre, plusRadOfY);
                ctx.lineTo(centre, centre);
                ctx.moveTo(centre, centre);
                ctx.fill(); //окрашиваем
                ctx.stroke(); //выводим
            }
        }
        // beforeDestroy() {
        //     clearInterval(this.rUpdatingIntervalId);
        // },
        // methods: {
        //     // trim(point, n) {
        //     //     return {
        //     //         x: (+point.x).toFixed(n),
        //     //         y: (+point.y).toFixed(n)
        //     //     }
        //     // }, normalizedToGraphCoords(point) {
        //     //     return {x: this.rSmooth * (point.x - .5) / .4, y: -this.rSmooth * (point.y - .5) / .4}
        //     // },
        //     // graphToNormalizedCoords(point) {
        //     //     return {x: (point.x * 0.4 / this.rSmooth + 0.5), y: (-point.y * 0.4 / this.rSmooth + 0.5)}
        //     // },
        //     // updateR() {
        //     //     this.rSmooth += (this.r - this.rSmooth) / 10;
        //     // },
        //     // onDrag(event) {
        //     //     if (!this.r || !this.mouseDownFlag)
        //     //         return;
        //     //     let {layerX: elemX, layerY: elemY} = event;
        //     //     let {scrollWidth: maxX, scrollHeight: maxY} = this.$refs['canvas'];
        //     //     let normalized = {x: elemX / maxX, y: elemY / maxY};
        //     //     this.$emit('input', this.trim(this.normalizedToGraphCoords(normalized), 2));
        //     //     this.render();
        //     // },
        //     onClick(event) {
        //         if (!this.r)
        //             return;
        //         let {layerX: elemX, layerY: elemY} = event;
        //         let {scrollWidth: maxX, scrollHeight: maxY} = this.$refs['canvas'];
        //         let normalized = {x: elemX / maxX, y: elemY / maxY};
        //         this.$emit('input', this.trim(this.normalizedToGraphCoords(normalized), 2));
        //         setTimeout(() => {
        //             this.$emit('click');
        //         }, 0);
        //         this.render();
        //     },
        //     render() {
        //         let ctx = this.canvasContext;
        //         let {width, height} = this; // ????
        //         width = +width;
        //         height = +height;
        //         let scale = this.rSmooth.toFixed(2);
        //         ctx.clearRect(0, 0, width, height);
        //         ctx.fillStyle = "#349eeb";
        //         ctx.fillRect(0.5 * width, 0.1 * height, 0.2 * width, 0.4 * height);
        //         ctx.beginPath();
        //         ctx.mv(.5, .9);
        //         ctx.ln(.3, .5);
        //         ctx.ln(.9, .5);
        //         ctx.arc(0.5 * width, 0.5 * height, .4 * (width + height) / 2, 0, Math.PI / 2);
        //         ctx.fill();
        //         ctx.lineWidth = 3;
        //         ctx.strokeStyle = "black";
        //         ctx.fillStyle = "black";
        //         ctx.beginPath();
        //         ctx.mv(0, .5);
        //         ctx.ln(1, .5);
        //         ctx.ln(.97, .48);
        //         ctx.mv(1, .5);
        //         ctx.ln(.97, .52);
        //         ctx.mv(.1, .49);
        //         ctx.ln(.1, .51);
        //         ctx.mv(.3, .49);
        //         ctx.ln(.3, .51);
        //         ctx.mv(.7, .49);
        //         ctx.ln(.7, .51);
        //         ctx.mv(.9, .49);
        //         ctx.ln(.9, .51);
        //         ctx.mv(.5, 1);
        //         ctx.ln(.5, 0);
        //         ctx.ln(.48, .03);
        //         ctx.mv(.5, 0);
        //         ctx.ln(.52, .03);
        //         ctx.mv(.49, .9);
        //         ctx.ln(.51, .9);
        //         ctx.mv(.49, .7);
        //         ctx.ln(.51, .7);
        //         ctx.mv(.49, .3);
        //         ctx.ln(.51, .3);
        //         ctx.mv(.49, .1);
        //         ctx.ln(.51, .1);
        //         ctx.font = "48px Arial";
        //         ctx.textAlign = "center";
        //         ctx.textBaseline = "top";
        //         ctx.txt(scale ? (-scale).toPrecision(2) : "-R", .1, .52);
        //         ctx.txt(scale ? (-scale / 2).toPrecision(2) : "-R/2", .3, .52);
        //         ctx.txt(scale ? (scale / 2).toPrecision(2) : "R/2", .7, .52);
        //         ctx.txt(scale ? (+scale).toPrecision(2) : "R", .9, .52);
        //         ctx.txt("x", .97, .52);
        //         ctx.textAlign = "left";
        //         ctx.textBaseline = "middle";
        //         ctx.txt(scale ? (-scale).toPrecision(2) : "-R", .52, .9);
        //         ctx.txt(scale ? (-scale / 2).toPrecision(2) : "-R/2", .52, .7);
        //         ctx.txt(scale ? (scale / 2).toPrecision(2) : "R/2", .52, .3);
        //         ctx.txt(scale ? (+scale).toPrecision(2) : "R", .52, .1);
        //         ctx.txt("y", .52, .03);
        //         ctx.stroke();
        //         if (scale) {
        //             for (let i = 0; i < this.prevResults.length; i++) {
        //                 let current = this.prevResults[i];
        //                 ctx.fillStyle = current.hit ? "green" : "red";
        //                 let {x, y} = this.graphToNormalizedCoords(current);
        //                 ctx.beginPath();
        //                 ctx.arc(x * width, y * height, .005 * (width + height) / 2, 0, 2 * Math.PI);
        //                 ctx.fill();
        //             }
        //         }
        //         if (this.value && this.value.x !== null && this.value.y !== null) {
        //             ctx.fillStyle = "#aa0000";
        //             ctx.strokeStyle = "#aa0000";
        //             let {x, y} = this.graphToNormalizedCoords(this.value);
        //             ctx.beginPath();
        //             ctx.arc(x * width, y * height, .005 * (width + height) / 2, 0, 2 * Math.PI);
        //             ctx.fill();
        //             ctx.beginPath();
        //             ctx.mv(x, y + .01);
        //             ctx.ln(x, y + .02);
        //             ctx.mv(x, y - .01);
        //             ctx.ln(x, y - .02);
        //             ctx.mv(x + .01, y);
        //             ctx.ln(x + .02, y);
        //             ctx.mv(x - .01, y);
        //             ctx.ln(x - .02, y);
        //             ctx.stroke();
        //             if (this.mouseDownFlag) {
        //                 ctx.fillStyle = "rgba(0, 0, 0, .75)";
        //                 ctx.font = "24px Arial";
        //                 ctx.textAlign = "left";
        //                 ctx.textBaseline = "bottom";
        //                 let text = `x: ${(+this.value.x).toFixed(2)} y: ${(+this.value.y).toFixed(2)}`;
        //                 ctx.fillRect(x * width + 10, y * height - 10, 35 + 9 * text.length, -50);
        //                 ctx.fillStyle = "white";
        //                 ctx.fillText(text, x * width + 20, y * height - 20)
        //             }
        //         }
        //     }
        // }
    }
</script>

<style scoped>
    .canvas {
        border-style: solid;
        border-color: forestgreen;
        border-width: 0.5px;
        display: block;
        margin: auto;
    }
</style>