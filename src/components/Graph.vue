<template>
    <div class="graph">
        <canvas class="canvas"
                ref="canvas"
                v-bind:width="$props.width"
                v-bind:height="$props.height"
                @click="check()"
        />


    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: "Graph",
        components: {},
        props: {
            dots: Array,
            width: Number,
            height: Number,
            value: Number,
            r: Number,
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
            dots() {
                this.draw();
                this.toDrawCirclesOnCanvas();
            }
        },
        data() {
            return {
                canvasContext: null,
                user: {
                    auth: false,
                    login: null,
                    password: null,
                    token: null,

                },
            }
        },

        mounted() {
            this.canvasContext = this.$refs.canvas.getContext('2d');
            this.draw();
        },
        methods: {//Тестовые методы для отрисовки
            createErrorToast: function (errorMessage, time) {
                this.$toasted.error(errorMessage).goAway(time);
            },
            check() { //todo сделать нейминг
                //todo БЫЛО ЗНАЧЕНИЕ document.getElementById('r').value; //берём значение радиуса из селекта ВЕРНУТЬ
                this.user.login = localStorage.getItem('user.login');
                this.user.password = localStorage.getItem('user.password');
                this.user.token = localStorage.getItem('user.token');
                this.user.auth = localStorage.getItem('user.auth');

                //todo сделать проверку


                let canvasElement = this.$refs.canvas;
                let domRect = canvasElement.getBoundingClientRect();

                let pixelX = (event.pageX - domRect.left - domRect.width / 2 - window.scrollX); //Пиксели, куда нажал пользователь на холст
                let pixelY = -(event.pageY - domRect.top - domRect.height / 2 - window.scrollY);//Добавил минус, потому что Y растёт вниз


                //todo если честно до я не доконца разобрался с этими формулами, хотя составил их сам, эмпирическим путём
                let x = pixelX / (100 / 2.0); //Перевод в вещественную координату
                let y = pixelY / (100 / 2.0);//100 пикселей, это шаг мой; 3 это граница по R


                x = Math.round(x * 100) / 100.0; //Округляем
                y = Math.round(y * 100) / 100.0;

                if (this.r < 0) {
                    this.createErrorToast('Выберите R >0!', 3000);
                } else if (this.r ==='') {
                    this.createErrorToast('Выберите R!', 3000);
                } else {
                    axios.post('http://192.168.1.42:8080/api/dots/add', {
                        x: x,
                        y: y,
                        r: this.r,
                    }, {
                        headers: {
                            'Content-Type': 'application/json',
                            'Authorization': this.user.token,
                        }
                    })
                        .then(() => {//при ответе от сервера

                            this.$emit('updateTable');
                            this.draw();
                        })
                        .catch((error) => {
                            if (error.response) {//при ошибке от сервера

                                let statusFromServer = error.response.status;
                                if (statusFromServer === 401) {

                                    //Срабатывает
                                    this.createErrorToast('Неправильный логин или пароль!', 3000);
                                } else if (statusFromServer === 400) {

                                    this.createErrorToast('Пустой логин или пароль!', 3000);
                                }

                            } else if (error.request) {//при ошибке запроса
                                this.createErrorToast('Вы оффлайн, проверьте соединение с интернетом!', 3000);
                            } else {
                                this.createErrorToast('Неизвестная ошибка!', 3000);
                            }
                            this.createErrorToast('Ошибка!', 3000);
                        });
                }


            },
            checkDot(x, y, r) {
                if (x <= 0 && y <= 0 && x >= -r && y >= -r / 2) {
                    return true;
                } else if (x <= 0 && y >= 0 && y <= 2 * x + (+r)) { // приводим R r числу
                    return true;
                } else if (x >= 0 && y <= 0 && x * x + y * y < r * r / 4) {
                    return true;
                }
                return false;
            },
            toDrawCirclesOnCanvas() {
                let ctx = this.$refs.canvas.getContext('2d');
                for (let i = 0; i < this.dots.length; i++) {

                    let x = this.dots[i].newX;
                    let y = this.dots[i].newY;

                    let pixelX = x * 100 / 2.0;
                    let pixelY = y * 100 / 2.0;


                    let resultForCanvas = this.checkDot(x, y, this.$props.r);

                    pixelX = pixelX + 125;
                    pixelY = pixelY - 125;
                    pixelY = -pixelY;

                    ctx.beginPath();
                    //todo оптимизировать
                    if (resultForCanvas) {

                        ctx.strokeStyle = "green";
                        ctx.fillStyle = "green";

                    } else {
                        ctx.strokeStyle = "red";
                        ctx.fillStyle = "red";
                    }
                    ctx.arc(pixelX, pixelY, 2.5, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.stroke();
                    ctx.closePath();
                }

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

                let marking = width / 50;


                ctx.beginPath();


                //todo временные фигуры
                ctx.strokeStyle = 'forestgreen';
                ctx.fillStyle = 'purple';
                ctx.lineWidth = 1;

                //1) Рисуем фигуры
                //1.1)Прямоугольник в третьей четверти
                ctx.rect(centre, centre, -radius, radius / 2);
                //1.2)Арка в четвёртой четверти
                ctx.arc(centre, centre, radius / 2, 0, Math.PI / 2, false);
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
        }
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