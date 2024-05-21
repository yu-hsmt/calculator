<template>
    <div class="phone">
        <div class="top">
            <button @click="showHistory" class="history"></button><!-- クリックで「showHsitory」を実行 -->
            <button @click="deleteValue" class="delete"></button><!-- クリックで「deleteValue」を実行 -->
        </div>

        <ul v-if="isHistory" class="historyDetails"><!-- 「isHistory」がtrueだったらこのulタグを表示 -->
            <li :key="operation.operations" v-for="operation in historyData"><!-- ？？？operationとは？ -->
                <small>{{ operation.operations }}</small><!-- ？？？ -->
                <h2>{{ operation.sum }}</h2><!-- ？？？ -->
            </li>
        </ul>

        <div class="middle">
            <small v-if="selectedOperation"></small><!-- ？？？ -->
            <div>
                <h1>{{ currentNum }}</h1><!-- currentNumを表示 -->
            </div>
        </div>

        <div class="bottom">
            <button @click="pressed('c')">c</button>
            <button @click="pressed('()')">()</button>
            <button @click="pressed('%')">%</button>
            <button @click="pressed('/')">/</button>

            <button @click="pressed('7')">7</button>
            <button @click="pressed('8')">8</button>
            <button @click="pressed('9')">9</button>
            <button @click="pressed('*')">*</button>

            <button @click="pressed('4')">4</button>
            <button @click="pressed('5')">5</button>
            <button @click="pressed('6')">6</button>
            <button @click="pressed('-')">-</button>

            <button @click="pressed('1')">1</button>
            <button @click="pressed('2')">2</button>
            <button @click="pressed('3')">3</button>
            <button @click="pressed('+')">+</button>

            <button @click="nedateValue">+/-</button>
            <button @click="pressed('0')">0</button>
            <button @click="pressed('.')">.</button>
            <button @click="pressed('=')">=</button>
        </div>
    </div>
</template>

<script>
    import { ref, onMounted } from "vue"; //Composition APIを使えるようにする

    export default { //他のファイルでも動くようにする
        setup() {
            const operations = ["+", "-", "*", "/", "%", "()"] //カッコ内を「operations」とする
            const numbers = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "."] //カッコ内を「numbers」とする
            const currentNum = ref("") //「currentNum」をリアクティブな変数とする
            const prevNum = ref("") //「prevNum」をリアクティブな変数とする
            const selectedOperation = ref("") //「selectedOperation」をリアクティブな変数とする
            const total = ref("") //「total」をリアクティブな変数とする
            const isHistory = ref(false) //「isHsitory」をfalseとする
            const historyData = [] //「historyData」を空の配列とする

            const pressed = (value) => { //valueを引数として、以下を「pressed」とする
                if (value === "=" || value === "Enter") { //valueが「=」または「Enter」だった場合、
                    historyData.push({ //配列「historyData」に以下を追加
                        operations: `${prevNum.value} ${selectedOperation.value} ${currentNum.value}`, //「operations」に「prevNumの値 selectedOperationの値 currentNumの値」
                        sum: eval(`${prevNum.value} ${selectedOperation.value} ${currentNum.value}`) //「sum」に「prevNumの値 selectedOperationの値 currentNumの値を実行したもの」
                    })
                    calculate(); //「calculate」を実行
                }
                else if (value === "%") percentage(); //valueが「%」だった場合、「percentage()」を実行
                else if (value === "c") clear(); //valueが「c」だった場合、「clear()」を実行
                else if (operations.includes(value)) applyOperation(value); //配列「operations」にvalueが含まれている場合（=演算子か%か()を押した場合）、valueを引数として「applyOperation()」を実行
                else if (numbers.includes(value)) appendNumber(value); //配列「numbers」にvalueが含まれている場合（=数字を押した場合）、valueを引数として「appendNumber()」を実行
            }

            const appendNumber = (value) => { //valueを引数として、以下を「appendNumber」とする
                currentNum.value = currentNum.value + value //「currentNum」の値にvalue（=押した数字）を（文字列として）追加_______？
            }

            const applyOperation = (value) => { //valueを引数として、以下を「applyOperation」とする
                calculate() //「calculate()」を実行
                prevNum.value = currentNum.value //prevNumの値をcurrentNumの値にする
                currentNum.value = "" //currentNumの値を初期化
                selectedOperation.value = value //selectedOperationの値をvalue（=押した演算子か%か()）にする
            }

            const calculate = () => { //以下を「calculate」とする
                if (selectedOperation.value === "*") multiply() //selectedOperationの値が「*」の場合、「multiply()」を実行
                if (selectedOperation.value === "()") multiply() //selectedOperationの値が「()」の場合、「multiply()」を実行
                else if (selectedOperation.value === "/") divide() //selectedOperationの値が「/」の場合、「divide()」を実行
                else if (selectedOperation.value === "%") percentage() //selectedOperationの値が「%」の場合、「precentage()」を実行
                else if (selectedOperation.value === "-") subtract() //selectedOperationの値が「-」の場合、「subtract()」を実行
                else if (selectedOperation.value === "+") sum() //selectedOperationの値が「+」の場合、「sum()」を実行
                prevNum.value = "" //prevNumの値を初期化
                selectedOperation.value = "" //selectedOperationの値を初期化
            }

            const multiply = () => { //以下の内容を「multiply」とする
                currentNum.value = prevNum.value * currentNum.value //currentNumの値をprevNum×currentNumの値とする
                total.value = currentNum.value //totalの値をcurrentNumの値とする_______なぜこれが必要？(以下同)
            }

            const divide = () => {
                currentNum.value = prevNum.value / currentNum.value //currentNumの値をprevNum÷currentNumの値とする
                total.value = currentNum.value //totalの値をcurrentNumの値とする
            }

            const percentage = () => {
                currentNum.value = currentNum.value / 100 //currentNumの値を100で割る
                total.value = currentNum.value //totalの値をcurrentNumの値とする
            }

            const subtract = () => {
                currentNum.value = prevNum.value - currentNum.value; //currentNumの値をprevNum-currentNumの値とする_________なぜここは「;」がある？
                total.value = currentNum.value //totalの値をcurrentNumの値とする
            }

            const sum = () => {
                currentNum.value = +prevNum.value + +currentNum.value; //currentNumの値をprevNum+currentNumの値とする_________なぜここは「;」がある/値の前に「+」がある？
                total.value = currentNum.value //totalの値をcurrentNumの値とする
            }

            const clear = () => { //以下を「clear」とする
                currentNum.value = "" //currentNumの値を空にする
                prevNum.value = "" //prevNumの値を空にする
                selectedOperation.value = "" //selectedOperationの値を空にする
                total.value = "" //totalの値を空にする
            }

            const deleteValue = () => { //以下をdeleteValueとする
                if (currentNum.value.length !== 0 && currentNum.value !== "0") { //currentNumの数が0でない（=何かしらの値が入っている）、かつcurrentNumの値が「0」でない場合
                    currentNum.value = currentNum.value.slice(0, -1) //currentNumの値を1文字目〜最後の文字まで切り出す_______なぜこれが必要？
                }
            }

            const negateValue = () => { //以下をnegateValueとする
                if (currentNum.value === '0') { //currentNumの値が「0」の場合、
                    return currentNum.value = '-0' //currentNumの値を「-0」にする
                }
                if (currentNum.value === '-') { //currentNumの値が「-」の場合、
                    return currentNum.value = '0' //currentNumの値を「0」にする
                }
                if (currentNum.value === '0.') { //currentNumの値が「0.」の場合、
                    return currentNum.value = `-${currentNum.value}` //currentNumの値を「-（currentNumの値）」にする_______なぜ「-0.」でないのか？
                }
                if (currentNum.value === '-0.') { //currentNumの値が「-0.」の場合、
                    return currentNum.value = '0.' //currentNumの値を「0.」にする
                }
                currentNum.value = `${currentNum.value * -1}` //currentNumの値×-1をしたものをcurrentNumとする_______なぜこれだけではダメなのか？
            }

            const showHistory = () => { //以下をshowHistoryとする
                isHistory.value = !isHistory.value //isHistoryの値を逆(true/false)にする
            }

            const handleKeydown = (event) => { //eventを引数として、以下をhandleKeydownとする
                pressed(event.key) //_____？？？
            }

            onMounted(() => { //以下をonMountedとする
                window.addEventListener('keydown', handleKeydown) //キーボード操作があるたびに「handleKeydown」を実行______あってる？？？
            })

            return {
                currentNum,
                pressed,
                selectedOperation,
                prevNum,
                negateValue,
                deleteValue,
                total,
                historyData,
                isHistory,
                showHistory
            }

        }
    }
</script>

<style scoped>
.phone {
    background-color: #6E3DD6;
    width: 20rem;
    /* height: 70vh; */
    border-radius: 2rem;
    margin: auto;
    position: relative;
}
.top {
    display: flex;
    justify-content: space-between;
    margin-bottom: 2rem;
    padding: 1rem;

}
.history {
    background: url('../../assets/history.svg') no-repeat top left;
    height: 2rem;
    width: 2rem;
}
.historyDetails {
    position: absolute;
    bottom: 0px;
    left: 0;
    background-color: #fff;
    padding: 1.4rem 1rem;
    border-radius: 2rem;
    height: 23rem;
    width: 20rem;
    z-index: 999;
    overflow-y: scroll;
}
.historyDetails li {
    border-bottom: 1px solid gray;
    padding-bottom: 10px;
}
.delete {
    background: url('../../assets/delete.svg') no-repeat top left;
    height: 2rem;
    width: 2rem;
}
.middle {
    text-align: center;
    margin-bottom: 3rem;
    height: 6rem;
}
.middle div{
    overflow-x: scroll;
    margin-top: 1rem;
}
.middle small {
    font-weight: bold;
    color: #B9A3EC;
    font-size: .75rem;
}
.middle h1 {
    color: white;
    font-size: 2.5rem;
    font-weight: 600;
    letter-spacing: -1px;
}

.bottom {
    background-color: #fff;
    padding: 1.4rem 1rem;
    border-radius: 2rem;
    font-size: 1.4rem;
    height: 23rem;
    display: grid;
    grid-template-columns: repeat(auto-fit,minmax(4rem,1fr));
    grid-gap: .5rem;
}
.bottom button {
    background-color: #E6E7E8;
    padding-top: 12px;
    border-radius: 1.5rem;
    font-size: 1.65rem;
    display: flex;
    justify-content: center;
    align-content: center;
    color: #808285;
}
.bottom button:nth-child(1) {
    background-color: #FA386B;
    color: white;
    padding-top: 10px;
}
.bottom button:nth-child(2) {
    color: #231F20;
}
.bottom button:nth-child(3) {
    color: #231F20;
}
.bottom button:nth-child(4) {
    color: #231F20;
}
.bottom button:nth-child(8) {
    color: #231F20;
}
.bottom button:nth-child(12) {
    color: #231F20;
}
.bottom button:nth-child(16) {
    color: #231F20;
}
.bottom button:nth-child(20) {
    background-color: #30C117;
    color: white;
}

button:focus {
    outline: none;
}
</style>