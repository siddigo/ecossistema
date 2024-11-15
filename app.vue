<template>
  <div>
    <button @click="restartGame">Reset</button>
    <button @click="addObject('wolf')">Add Lobo</button>
    <button @click="addObject('rabbit')">Add Coelho</button>
    <button @click="addObject('plant')">Add Planta</button>
    <label>Parar quando faltar animal
      <input v-model="STOP_WHEN_ZERO" type="checkbox">
    </label>
    
    <div id="container">

    </div>
  </div>
</template>
<script setup>
  // @ts-check
/*

ETAPA 1 - OK

Spawnar uma quantidade x de plantas, y de coelhos e z de lobos - OK

plantas:
    se reproduzem sozinhas, com um raio grande para o filho - OK
    nao comem - OK

coelhos:
    comem plantas - OK
    se reproduzem com outros coelhos - OK

lobos:
    comem coelhos - OK
    se reproduzem com outros lobos - OK

*/


/*

ETAPA 2

    Adicionar um timer - contador de dias - OK

    Adicionar um contador de animais - OK

    Criar uma expectativa de vida para as entidades - OK

    As entidades devem morrer ao final da sua vida - OK


*/

/*

ETAPA 3 - CONTROLE DE VIDA

    Animais possuem stamina - ok
        Comer dá stamina    - ok
        Outras atividades (andar, reproduzir, ect) gastam stamina   - ok

    Stamina zero = morte. - ok

*/

/*

ETAPA 3.5 - GRÁFICO

    Gráfico pra analisar os objetos no tempo



*/

/*

ETAPA 4 - MELHORIA DE MOVIMENTACAO

    Animais devem decidir um ponto final (no chunck) para visitar
        -Desviar de objetos e continuar o caminho, caso colidam
    
    Inserção do range de visão para animais
        -Se toparem com um parceiro e puderem reproduzir
            -Andam até o parceiro
                -reproduzem
        -Se toparem com um alimento e puderem comer, comem
            -Andam até a comida
                -comem

    Adição do sentido de predador
        -Se topar com um predador
            -anda na direção oposta

    Adição de tempo para reproduzir/comer
        -Enquanto estão reproduzindo/comendo, não andam
    
    Adição da habilidade de "correr"
        -Correr gasta mais energia, vai mais rapido
            -Correr usa folego, que se recarrega com o tempo


*/

/* 

ETAPA 5 - ADICAO DE TESTES ~ REFATORAR ~

*/

/*

ETAPA 6 - PERSONAGEM

*/

/*

ETAPA 7 - ESTUDAR WORKER 

*/


/* 

ETAPA 8 - CRIAÇÃO DE CHUNCKS

*/

/*

ETAPA 9 - MELHORIA DO SPAWN

*/


/* ESTAPA X - IA? */
let addObject;
let restartGame;
import {onMounted,ref} from "vue";
const STOP_WHEN_ZERO = ref(true);
onMounted(()=>{

restartGame = ()=> window.location.reload();

// Tela
console.log(document);
console.log(window);
const container = document.getElementById('container');

// Canvas Game
const canvas = document.createElement('canvas');
canvas.id = 'game';
const ctx = canvas.getContext('2d');
canvas.width = 1000;
canvas.height = 1000;
container.appendChild(canvas);
if (!ctx) { throw new Error("Por algum movito o contexto do gráfico não funcionou"); }

/*// Canvas Gráfico
const canvasGraph = document.createElement('canvas');
canvasGraph.id = 'graph';
const ctxGraph = canvasGraph.getContext('2d');
canvasGraph.width = 1000;
canvasGraph.height = 1000;
container.appendChild(canvasGraph);

if (!ctxGraph) { throw new Error("Por algum movito o contexto do gráfico não funcionou"); }
ctxGraph.fillStyle = "black";
ctxGraph.fillRect(0, 0, canvasGraph.width, canvasGraph.height);
*/
// Estilização da tela
container.style.display = 'flex';
container.style.justifyContent = 'space-between';
container.style.width = '100%';

// Inicialização de variáveis
const DEFAULT_OBJECT = {
    movement: {
        speed: 0,
        staminaForMovement: 0,
    },
    appearence: {
        initialSize: 0,
        standardColor: 'white',
        variationColor: 'black',
    },
    life: {
        maxStamina: 0,
        initialStamina: 0,
        lifeExpectation: 0,
    },
    reprodution: {
        daysToReproduce: 0,
        staminaForReprodution: 0,
    },
    energyValue: 0,
    food: [],
};

/**
 * @typedef {Object} Entidade
 * @property {{
 *   speed: number,
 *   staminaForMovement: number,
 * }} movement
 * @property {{
 *   initialSize: number,
 * }} appearence
 */

/**
 * @typedef {Object} ObjectList
 * @property {Entidade} plant
 * @property {Entidade} rabbit
 * @property {Entidade} wolf
 */

/**
 * @type {ObjectList}
 */
const TYPES_OBJECT = {
    'plant': {
        movement: {
            speed: 0,
            staminaForMovement: 0,
            visionRadius:0,
        },
        appearence: {
            initialSize: 10,
            standardColor: 'rgb(1,148,27)',
            variationColor: 'rgb(1,255,47)',
        },
        life: {
            maxStamina: 0,
            initialStamina: 2,
            lifeExpectation: 10,
            baseStaminaConsume: 0,
        },
        reprodution: {
            daysToReproduce: 2,
            staminaForReprodution: 0,
            soloReprodution: true,
            reprodutionRadiusFactor: 100,
        },
        energyValue: 75,
        food: [],
        ignoreCollision: ['wolf'],
    },
    'rabbit': {
        movement: {
            speed: 100,
            staminaForMovement: 0.005,
            visionRadius:75,
        },
        appearence: {
            initialSize: 15,
            standardColor: 'rgb(97,52,30)',
            variationColor: 'rgb(173,93,54)',
        },
        life: {
            maxStamina: 100,
            initialStamina: 100,
            lifeExpectation: 60,
            baseStaminaConsume: 1,
        },
        reprodution: {
            daysToReproduce: 3,
            staminaForReprodution: 10,
            soloReprodution: false,
            reprodutionRadiusFactor: 2,
        },
        energyValue: 160,
        food: ['plant'],
        ignoreCollision: [],
    },
    'wolf': {
        movement: {
            speed: 100,
            staminaForMovement: 0.01,
            visionRadius:125,
        },
        appearence: {
            initialSize: 25,
            standardColor: 'rgb(115,111,96)',
            variationColor: 'rgb(173,168,144)',
        },
        life: {
            maxStamina: 200,
            initialStamina: 200,
            lifeExpectation: 120,
            baseStaminaConsume: 1,
        },
        reprodution: {
            daysToReproduce: 10,
            staminaForReprodution: 50,
            soloReprodution: false,
            reprodutionRadiusFactor: 2,
        },
        energyValue: 300,
        food: ['rabbit'],
        ignoreCollision: ['plant'],
    },
};

const SPAWN_LIST = {
    'plant': 0.87,
    'rabbit': 0.12,
    'wolf': 0.01,
};

const MAX_MOBS = 500;

const DECISION_LIST = {
    'move': 0.99,
    'change': 0.005,
    'stop': 0.005,
};

const OBJECTS_SPAWN_COUNT = 100;

const plants = [];
const rabbits = [];
const wolfs = [];

const objects = [];
const objectsCount = [];

const objectsBorn = [];
const objectsEat = [];
const objectsDied = [];
const objectsEnergyOut = [];

let countPlant = 0;
let countRabbit = 0;
let countWolf = 0;

let timer = 0;
let init = 0;

let tickTime=0

const taxaQuadros = 60;

function reset(){

}

addObject=(type)=> createObject(canvas.width / 2, canvas.height / 2, Math.min(canvas.width / 2, canvas.height / 2), type, true, true);

// O jogo
newGame(ctx);

/**
 * Responsável por inicializar um novo jogo
 * @param {CanvasRenderingContext2D} ctx canvas
 */
function newGame(ctx) {
    initializeObjects(ctx);
    game(ctx);
}

/**
 * Responsável por atualizar o jogo
 * @param {CanvasRenderingContext2D} ctx canvas
 */
function game(ctx) {
    tick();
    render(ctx);
    requestAnimationFrame(() => { game(ctx) });
}

function tick() {

    if (STOP_WHEN_ZERO.value) {
        for (let type in objectsCount) {
            if (objectsCount[type] == 0) return;
        }
    }

    objects.forEach((object) => {

        //TODO Tick object (update ->gain size, alter collor, kill, act)
        updateObject(object);

        const outOfStamina = object.life.stamina <= 0;
        const tooMuchOld = TYPES_OBJECT[object.type].life.lifeExpectation < object.life.age;
        if (tooMuchOld || outOfStamina) {
            if (outOfStamina) { objectsEnergyOut[object.type]++ };
            if (tooMuchOld) { objectsDied[object.type]++ };
            kill(object);
        } else {
            act(object);
            updateObject(object);
        }

    });

    timer = (Math.round((Date.now() - init) / 1000));
    tickTime=Date.now();

}

// Cálculos e Física

function collision(mainObject) {

    // Objects collision
    for (let secondaryObject of objects) {

        if (mainObject === secondaryObject) continue;
        if (TYPES_OBJECT[mainObject.type].ignoreCollision.includes(secondaryObject.type)) { continue; }

        if (isOverlap(mainObject, secondaryObject)) {

            if (TYPES_OBJECT[mainObject.type].food.includes(secondaryObject.type) && !mainObject.life.canEat) { continue; }

            return { isCollided: true, object: secondaryObject }
        }

    };

    // World collision, can be better
    if ((mainObject.position.x - mainObject.appearence.size / 2) <= 0) {
        return { isCollided: true, object: mainObject }
    }

    if ((mainObject.position.x + mainObject.appearence.size / 2) >= canvas.width) {
        return { isCollided: true, object: mainObject }
    }

    if ((mainObject.position.y - mainObject.appearence.size / 2) <= 0) {
        return { isCollided: true, object: mainObject }
    }

    if ((mainObject.position.y + mainObject.appearence.size / 2) >= canvas.height) {
        return { isCollided: true, object: mainObject }
    }

    return { isCollided: false, object: mainObject }
}

function calcStartPosition(centerSpawnX, centerSpawnY, radiusSpawn) {

    const cortinateSpawnX = centerSpawnX + (Math.random() * radiusSpawn * 2) - radiusSpawn
    const cortinateSpawnY = centerSpawnY + (Math.random() * radiusSpawn * 2) - radiusSpawn

    const positon = {
        x: cortinateSpawnX,
        y: cortinateSpawnY
    }

    return positon
}

function draft(chanceList) {

    const value = Math.random();
    let aggregatedValue = 0;

    for (let ticket in chanceList) {
        let chance = chanceList[ticket];
        aggregatedValue += chance;

        if (value <= aggregatedValue) {
            return ticket;
        }

    }

    return '';
}

function calcularDistancia(p1, p2) {
    const dx = p1.x - p2.x;
    const dy = p1.y - p2.y;
    return Math.sqrt(dx ** 2 + dy ** 2);
}

function isOverlap(object1, object2) {

    const center1 = object1.position;
    const center2 = object2.position;

    const dx = center1.x - center2.x;
    const dy = center1.y - center2.y;

    const squaredDistance = dx ** 2 + dy ** 2;

    const radius1 = object1.appearence.size / 2;
    const radius2 = object2.appearence.size / 2;

    const squaredRadius = (radius1 + radius2) ** 2

    return (squaredRadius >= squaredDistance);

}

function becomeOverlap(object1, object2) {

    const center1 = object1.cache.nextPosition;
    const center2 = object2.position;

    const dx = center1.x - center2.x;
    const dy = center1.y - center2.y;

    const squaredDistance = dx ** 2 + dy ** 2;

    const radius1 = object1.appearence.size / 2;
    const radius2 = object2.appearence.size / 2;

    const squaredRadius = (radius1 + radius2) ** 2

    return (squaredRadius >= squaredDistance);

}
// Objetos

/**
 * Responsável por inicializar os cabra do jogo
 * @param {CanvasRenderingContext2D} ctx canvas
 */
function initializeObjects(ctx) {

    init = Date.now();
    tickTime=init;

    for (let type in TYPES_OBJECT) {
        objectsBorn[type] = 0;
        objectsEat[type] = 0;
        objectsDied[type] = 0;
        objectsEnergyOut[type] = 0;
    }
       
    //Cria pelo menos 2 de cada espécie
    for (let type in SPAWN_LIST) {
        createObject(canvas.width / 2, canvas.height / 2, Math.min(canvas.width / 2, canvas.height / 2), type, true, true);
        createObject(canvas.width / 2, canvas.height / 2, Math.min(canvas.width / 2, canvas.height / 2), type, true, true);
    }

    const spawnNumber=OBJECTS_SPAWN_COUNT-objectsCount.length;

    // Cria os demais
    for (let i = 0; i < spawnNumber; i++) {

        const type = draft(SPAWN_LIST);
        createObject(canvas.width / 2, canvas.height / 2, Math.min(canvas.width / 2, canvas.height / 2), type, true, true);
    }
    render(ctx);
}

function newObject(cordinateSpawnX, cordinateSpawnY, radiusSpawn, type, randomAge) {

    let spawnTime = Date.now();
    let lastReproduce = Date.now();
    let canReproduce = false;

    if (randomAge) {
        const totalLifeTime = TYPES_OBJECT[type].life.lifeExpectation * 24000;
        const minSpawnTime = spawnTime - totalLifeTime;
        spawnTime = Math.random() * (spawnTime - minSpawnTime) + minSpawnTime;

        const totalTimeToReproduce = TYPES_OBJECT[type].reprodution.daysToReproduce * 24000
        const timeSinceLastReproduce = Math.random() * totalTimeToReproduce
        lastReproduce = Date.now() - timeSinceLastReproduce;

    }

    const object = {};

    object.type = type;
    object.position = calcStartPosition(cordinateSpawnX, cordinateSpawnY, (radiusSpawn - TYPES_OBJECT[type].appearence.initialSize));
    object.movement = {
        direction: { x: 0, y: 0 },
        objective: { x: 0, y: 0 }
    };
    object.appearence = {
        color: TYPES_OBJECT[type].appearence.standardColor,
        size: TYPES_OBJECT[type].appearence.initialSize,
    };
    object.life = {
        spawnTime: spawnTime,
        stamina: TYPES_OBJECT[type].life.initialStamina,
        age: 0,
        canEat: true,
    };
    object.reprodution = {
        canReproduce: canReproduce,
        lastReproduce: lastReproduce,
    };
    object.cache = {
        nextPosition: { x: 0, y: 0 },
        lastPosition: { x: 0, y: 0 },
        lastCollision: { isCollided: false, object: object },
        objectView: { isCollided: false, object: object },
    };

    updateAge(object);
    updateCanReproduce(object);
    updateCanEat(object);

    return object
}

function updateAge(object) {

    const now = Date.now();
    const totalLifeTime = now - object.life.spawnTime;
    const age = Math.trunc(totalLifeTime / 24000);

    object.life.age = age;
}

function updateCanReproduce(object) {

    //Valida se o limite já foi atingido
    if (objectsCount[object.type] > MAX_MOBS) {

        object.reprodution.canReproduce = false;
        object.reprodution.color = TYPES_OBJECT[object.type].appearence.standardColor;

        return;
    }

    //Valida se tem energia para reproduzir
    if (object.life.stamina < TYPES_OBJECT[object.type].reprodution.staminaForReprodution) {

        object.reprodution.canReproduce = false;
        object.reprodution.color = TYPES_OBJECT[object.type].appearence.standardColor;

        return;
    }

    //Obtém os dados da última reproducao e calcula o período para a próxima
    const now = Date.now();
    const totalReprodutionGap = TYPES_OBJECT[object.type].reprodution.daysToReproduce * 24000;
    const nextReprodutionAvaliable = object.reprodution.lastReproduce + totalReprodutionGap;

    if (now >= nextReprodutionAvaliable) {
        object.reprodution.canReproduce = true;
        object.appearence.color = TYPES_OBJECT[object.type].appearence.variationColor;
    } else {
        object.reprodution.canReproduce = false;
        object.appearence.color = TYPES_OBJECT[object.type].appearence.standardColor;
    }
}

function updateCanEat(object) {

    //Valida se tem energia para reproduzir
    if (object.life.stamina < TYPES_OBJECT[object.type].life.maxStamina) {
        object.life.canEat = true;
    } else {
        object.life.canEat = false;
    }

}


function updateObject(object) {
    updateAge(object);
    updateCanReproduce(object);
    updateCanEat(object);  
    updateVision(object); 
    updateStamina(object);
}

function updateStamina(object){
    object.life.stamina-=TYPES_OBJECT[object.type].life.baseStaminaConsume*(Date.now()-tickTime)/1000;
}


function createObject(cordinateSpawnX, cordinateSpawnY, radiusSpawn, type, forceSpawn, randomAge) {

    const object = newObject(cordinateSpawnX, cordinateSpawnY, radiusSpawn, type, randomAge);

    if (collision(object).isCollided) {

        if (!forceSpawn) {
            return false;
        } else {
            while (collision(object).isCollided) {
                object.position = calcStartPosition(cordinateSpawnX, cordinateSpawnY, (radiusSpawn - object.appearence.size));
            }
        }
    }

    objects.push(object);

    if (objectsCount[object.type]) {
        objectsCount[object.type]++
    } else {
        objectsCount[object.type] = 1;
    }

    return true;
}

// Renderizar

/**
 * Responsável renderizar o canvas
 * @param {CanvasRenderingContext2D} ctx canvas
 */
function render(ctx) {
    renderBg(ctx);
    renderObjects();
    renderStats(ctx);
}

/**
 * Responsável por limpar o fundo do canvas
 * @param {CanvasRenderingContext2D} ctx canvas
 */
function renderBg(ctx) {
    ctx.fillStyle = "rgb(38,71,19)";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
}

/**
 * Responsável por renderizar as estetísticas
 * @param {CanvasRenderingContext2D} ctx canvas
 * @return {void}
 */
function renderStats(ctx) {
    ctx.fillStyle = 'black'
    ctx.textAlign = 'right';
    ctx.textBaseline = 'top';
    ctx.font = '32px Arial';
    const horas = timer % 24;
    const days = Math.trunc(timer / 24);
    let displayTempo;
    if (days > 0) { displayTempo = days + 'd' + horas + 'h'; } else { displayTempo = horas + 'h'; }
    ctx.fillText('Tempo: ' + displayTempo, canvas.width, 0);
    ctx.fillText('Plantas: ' + objectsCount.plant, canvas.width, 32);
    ctx.fillText('Lobos: ' + objectsCount.wolf, canvas.width, 64);
    ctx.fillText('Coelhos: ' + objectsCount.rabbit, canvas.width, 96);
    ctx.fillText('Born (P/C/L): ' + objectsBorn.plant + '/' + objectsBorn.rabbit + '/' + objectsBorn.wolf, canvas.width, 128);
    ctx.fillText('Eatted (P/C/L): ' + objectsEat.plant + '/' + objectsEat.rabbit + '/' + objectsEat.wolf, canvas.width, 160);
    ctx.fillText('Died (P/C/L): ' + objectsDied.plant + '/' + objectsDied.rabbit + '/' + objectsDied.wolf, canvas.width, 192);
    ctx.fillText('EnergyOut (P/C/L): ' + objectsEnergyOut.plant + '/' + objectsEnergyOut.rabbit + '/' + objectsEnergyOut.wolf, canvas.width, 224);
}

function renderObjects() {
    objects.forEach((object) => {
        renderObject(object);
    });
}

function renderObject(object) {
    ctx.beginPath();
    ctx.arc(object.position.x, object.position.y, object.appearence.size / 2, 0, 2 * Math.PI);
    ctx.fillStyle = object.appearence.color;
    ctx.fill();
    ctx.fillStyle = 'yellow'
    ctx.font = '8px Arial';
    ctx.textBaseline = 'center';
    ctx.textAlign = 'center';
    ctx.fillText(Math.trunc(object.life.age), object.position.x, object.position.y - 3);
    /*
    ctx.arc(object.position.x, object.position.y, object.movement.visionRadius, 0, 2 * Math.PI);
    ctx.fillStyle = object.appearence.color;
    ctx.stroke();
    */
}

// Ações

function act(object) {

    if (TYPES_OBJECT[object.type].movement.speed > 0) { move(object); }

    const decision = getDesicion(object);

    switch (decision) {
        case 'move':
            break;
        case 'reproduce':
            reproduce(object);
            break;
        case 'change':
            changeDirection(object);
            break;
        case 'stop':
            stop(object);
            break;
        case 'eat':
            eat(object);
            break;
        case 'goto':
            changeObjective(object);
            break;
        case 'goaway':
            goAway(object);
            break;
        default:
            // Caso não corresponda a nenhum dos valores anteriores (opcional)
            console.log('Ação desconhecida');
    }
}

function changeObjective(object){
    const destinationPoint = structuredClone(object.cache.objectView.object.position);
    object.movement.objective=destinationPoint;
}

function goAway(object){
    const nextX=(2*object.position.x-object.cache.objectView.object.position.x);
    const nextY=(2*object.position.y-object.cache.objectView.object.position.y);
    const destinationPoint = {x: nextX, y:nextY};
    object.movement.objective=destinationPoint;
}

function updateVision(mainObject){

    let minDistance=null;
    const collisionStatus= {isCollided: false, object: mainObject}

    
    // Objects vision
    for (let secondaryObject of objects) {

        let danger=false;

        if (mainObject === secondaryObject) continue;
        if (TYPES_OBJECT[mainObject.type].ignoreCollision.includes(secondaryObject.type)) { continue; }
        if (TYPES_OBJECT[mainObject.type].food.includes(secondaryObject.type) && !mainObject.life.canEat) { continue; }
        if ((secondaryObject.type ==mainObject.type) && !mainObject.reprodution.canReproduce) { continue; }
        if (TYPES_OBJECT[secondaryObject.type].food.includes(mainObject.type)) { danger=true; }

        if (isOverlap({position:mainObject.position, appearence:{size:TYPES_OBJECT[mainObject.type].movement.visionRadius*2}}, secondaryObject)) {

            const squaredDistance= (mainObject.position.x - secondaryObject.position.x)**2+(mainObject.position.y - secondaryObject.position.y)**2

            if (minDistance==null||minDistance>squaredDistance) {
                collisionStatus.isCollided=true;
                collisionStatus.object=secondaryObject;
                minDistance = danger ? 0 : squaredDistance;
            }

        }

    };
    /*const collisionStatus= {isCollided: false, object: mainObject}
    // Objects vision
    for (let secondaryObject of objects) {

        if (mainObject === secondaryObject) continue;
        if (TYPES_OBJECT[mainObject.type].ignoreCollision.includes(secondaryObject.type)) { continue; }
        if (TYPES_OBJECT[mainObject.type].food.includes(secondaryObject.type) && !mainObject.life.canEat) { continue; }
        if (TYPES_OBJECT[mainObject.type==secondaryObject.type) && !mainObject.life.canEat) { continue; }

        if (isOverlap({position:mainObject.position, appearence:{size:TYPES_OBJECT[mainObject.type].movement.visionRadius*2}}, secondaryObject)) {

            collisionStatus.isCollided=true;
            collisionStatus.object=secondaryObject;
        }

    };
    */
    mainObject.cache.objectView = collisionStatus;
}


// Tipar os retornos
function getDesicion(object) {

    // Se esbarrou em algo
    if (object.cache.lastCollision.isCollided) {

        // Se for no mundo, retorna ele mesmo na funcao - melhorar
        if (object.cache.lastCollision.object == object) {

            return 'change';

            // se for em outro do mesmo tipo, tenta reproduzir
        } else if (object.cache.lastCollision.object.type == object.type) {

            // Se os dois estão apotos para reproduzir, reproduzem, caso contrário, só voltam a andar
            if ((object.reprodution.canReproduce) && (object.cache.lastCollision.object.reprodution.canReproduce)) {
                return 'reproduce';
            } else { }
            return 'change';

            // Se for de um tipo de comida, tenta comer - não come se a stamina estiver no máximo 
        } else if (TYPES_OBJECT[object.type].food.includes(object.cache.lastCollision.object.type)) {

            if (object.life.canEat) {
                return 'eat';
            } else {
                return 'change';
            }
            // Se o objeto for comida de quem ele colidiu, ele corre
        } else if (TYPES_OBJECT[object.cache.lastCollision.object.type].food.includes(object.type)) {

            return 'change';

            // Se nada for verdadeiro, permanece parado para debug
        } else {
            return 'stop';
        }

        // Se não esbarrou
    } else {

        //Todo mudar a ideia se viu algo interessante
        if (object.cache.objectView.isCollided) {

            if (object.cache.objectView.object != object){

                if (TYPES_OBJECT[object.cache.objectView.object.type].food.includes(object.type)){
                    return 'goaway'
                }

                if ((object.reprodution.canReproduce) && (object.cache.objectView.object.reprodution.canReproduce)){
                    return 'goto'
                }

                if (TYPES_OBJECT[object.type].food.includes(object.cache.objectView.object.type)&&(object.life.canEat)){
                    return 'goto'
                }

            }

        }

        if ((TYPES_OBJECT[object.type].reprodution.soloReprodution) && (object.reprodution.canReproduce)) { return draft({ 'move': 0.99, 'reproduce': 0.01 }); }

        return draft(DECISION_LIST);
    }
}

function move(object) {

    object.cache.lastPosition = { ...object.position };

    const targetX=object.movement.objective.x;
    const targetY=object.movement.objective.y;

    const deltaX = targetX - object.position.x;
    const deltaY = targetY - object.position.y;

    if (deltaX === 0 && deltaY === 0) {
        return;
    }

    // Calcular direção e normalizá-la
    const magnitude = Math.sqrt(deltaX * deltaX + deltaY * deltaY);
    const dirX = deltaX / magnitude;
    const dirY = deltaY / magnitude;

    // Calcular o deslocamento baseado na velocidade
    const speed = TYPES_OBJECT[object.type].movement.speed / taxaQuadros;
    const moveX = dirX * speed;
    const moveY = dirY * speed;

    object.position.x += moveX;
    object.position.y += moveY;

    //Atualiza status de colisão
    updateCanEat(object);
    const collisionStatus = collision(object);

    object.cache.lastCollision = collisionStatus;

    if (collisionStatus.isCollided) {
        object.position = { ...object.cache.lastPosition };
    } else {
        //Atualiza consumo de energia
        object.life.stamina -= TYPES_OBJECT[object.type].movement.staminaForMovement * (Math.abs(moveX) + Math.abs(moveY));
    }

}

function changeDirection(object) {
    object.movement.direction.x = (Math.round(Math.random() * 2) - 1);
    object.movement.direction.y = (Math.round(Math.random() * 2) - 1);

    object.movement.objective.x = (Math.round(Math.random() * canvas.width));
    object.movement.objective.y = (Math.round(Math.random() * canvas.height));
}

function stop(object) {
    object.movement.direction.x = 0;
    object.movement.direction.y = 0;

    object.movement.objective.x = object.position.x;
    object.movement.objective.y = object.position.y;
}

function kill(object) {
    const index = objects.indexOf(object);
    if (index !== -1) {
        objectsCount[object.type]--;
        objects.splice(index, 1);
    }
}

function reproduce(object) {

    //Valida se pode reproduzir
    if (!object.reprodution.canReproduce) { return; }
    if (object.life.stamina < TYPES_OBJECT[object.type].reprodution.staminaForReprodution) { return; }

    // Inicializa dados para spawn do filhote
    let cordinateSpawnX;
    let cordinateSpawnY;
    const radiusSpawn = object.appearence.size * TYPES_OBJECT[object.type].reprodution.reprodutionRadiusFactor;

    //Se for um objeto de auto-reprodução (plantas), não precisa de colisão
    if (TYPES_OBJECT[object.type].reprodution.soloReprodution) {

        //Calcula os dados para spawn do filhote
        cordinateSpawnX = object.position.x;
        cordinateSpawnY = object.position.y;

    } else {

        //Pega os dados da última colisão
        const collisionStatus = object.cache.lastCollision;

        //Valida se realmente há um objeto próximo
        if (!collisionStatus.isCollided) { return; }

        const secondaryObject = collisionStatus.object

        //Valida se realmente o objeto não é a parede ou de outro tipo
        if (secondaryObject == object) { return; }
        if (secondaryObject.type != object.type) { return; }

        //Valida se o par podem reproduzir
        if (!secondaryObject.reprodution.canReproduce) { return; }
        if (secondaryObject.life.stamina < TYPES_OBJECT[secondaryObject.type].reprodution.staminaForReprodution) { return; }

        //Calcula os dados para spawn do filhote
        cordinateSpawnX = (object.position.x + secondaryObject.position.x) / 2;
        cordinateSpawnY = (object.position.y + secondaryObject.position.y) / 2;

    }

    const numberObjects = objectsCount[object.type];

    //Cria o filhote
    createObject(cordinateSpawnX, cordinateSpawnY, radiusSpawn, object.type, false, false);

    //Desvia se o filhote não teve espaço pra nascer
    if (numberObjects == objectsCount[object.type]) { return; }

    objectsBorn[object.type]++;

    //Consumo de stamina
    object.life.stamina -= TYPES_OBJECT[object.type].reprodution.staminaForReprodution;

    if (!TYPES_OBJECT[object.type].reprodution.soloReprodution) {
        const secondaryObject = object.cache.lastCollision.object
        secondaryObject.life.stamina -= TYPES_OBJECT[secondaryObject.type].reprodution.staminaForReprodution;
    }

    // Atualiza os dados de reprodução
    object.reprodution.lastReproduce = Date.now();
    updateCanReproduce(object);
    if (!TYPES_OBJECT[object.type].reprodution.soloReprodution) {
        const secondaryObject = object.cache.lastCollision.object
        secondaryObject.reprodution.lastReproduce = Date.now();
        updateCanReproduce(secondaryObject);
    }

    //Continuar Movimento
    changeDirection(object);

    if (!TYPES_OBJECT[object.type].reprodution.soloReprodution) {
        const secondaryObject = object.cache.lastCollision.object
        changeDirection(secondaryObject);
    }
}

function eat(object) {

    //Pega os dados da última colisão
    const collisionStatus = object.cache.lastCollision;

    //Valida se realmente há um objeto próximo
    if (!collisionStatus.isCollided) { return; }

    const secondaryObject = collisionStatus.object

    //Valida se realmente o objeto não é a parede ou de um tipo não comível
    if (secondaryObject == object) { return; }
    if (!TYPES_OBJECT[object.type].food.includes(secondaryObject.type)) { return; }

    //Valida a quantidade de energia
    if (object.life.stamina >= TYPES_OBJECT[object.type].life.maxStamina) { return; }

    //Ganho de stamina
    object.life.stamina += TYPES_OBJECT[secondaryObject.type].energyValue;

    //Mata o comido
    objectsEat[secondaryObject.type]++;
    kill(secondaryObject);

    //Volta a andar depois disso
    changeDirection(object);
}
});
</script>
