# lodash
Useful Library
```
const _ = require('lodash');
const moment = require('moment');

const numbers = [1, 2, 3, 4, 5, 6, 7, 8];

console.log('First:', _.first(numbers));
console.log('6th Number (index 6):', _.nth(numbers, 6));

const chunked = _.chunk(numbers, 4);
console.log('chunks by 4:', chunked);

const diff = _.difference(numbers, [1, 4, 5]);
console.log('difference:', diff);

const dropped = _.drop(numbers, 5);
console.log('Drop 5 Elements:', dropped);

const taken = _.take(numbers, 4);
console.log('Taken:', taken);

const filled = _.fill(Array(10), 2);
console.log('Fill:', filled);

const num = [1, 2, [2, [4]], [5]];
console.log('1st Level Flatten:', _.flatten(num));
console.log('Depth Level Flatten (1):', _.flattenDepth(num, 1));
console.log('Deep Level Flatten:', _.flattenDeep(num));

const c = [1, 2, 4];
const d = [2, 4, 5];

console.log('Union:', _.union(c, d));
console.log('Intersection:', _.intersection(c, d));


// -------------------- Employees --------------------

const employees = [
  { id: 10, first_name: 'Lucie', last_name: 'Gude', email: 'rjain@gmail.com', gender: 'Female',   salary: '$10.50' },
  { id: 11, first_name: 'Lucie', last_name: 'Gude', email: 'rjain@gmail.com', gender: 'Male',     salary: '$11.50' },
  { id: 12, first_name: 'Lucie', last_name: 'Gude', email: 'rjain@gmail.com', gender: 'Bigender', salary: '$12.50' },
  { id: 13, first_name: 'Lucie', last_name: 'Gude', email: 'rjain@gmail.com', gender: 'Female',   salary: '$13.50' }
];

console.log('CountBy:', _.countBy(employees, 'gender'));
console.log('GroupBy:', _.groupBy(employees, 'gender'));

const maxSalary = _.orderBy(
  employees,
  e => parseFloat(e.salary.replace('$', '')),
  'desc'
);

console.log('OrderBy (numeric salary):', maxSalary);

const top3 = _.take(maxSalary, 3);
console.log('Top 3:', top3);

const top3Names = _.map(top3, 'first_name');
console.log('Top3Names:', top3Names);

const totalSalary = _.reduce(
  employees,
  (prev, curr) => prev + parseFloat(curr.salary.replace('$', '')),
  0
);

console.log('TotalSalary:', totalSalary);

const grpByGender = _.groupBy(employees, 'gender');

const salaryByGender = _.mapValues(grpByGender, grp =>
  _.sumBy(grp, e => parseFloat(e.salary.replace('$', '')))
);

console.log('Salary By Gender:', salaryByGender);


// -------------------- Travel Needs --------------------

const travelNeeds = {
  travelNeeds: [
    { id: 19,  count: 1, travelNeedTypeName: 'ambulatory', passengerTypeName: 'client' },
    { id: 20,  count: 1, travelNeedTypeName: 'wheelchair' },
    { id: 192, count: 1, travelNeedTypeName: 'crutches' },
    { id: 22,  count: 1, travelNeedTypeName: 'ambulatory', passengerTypeName: 'client' }
  ]
};

function getTravelNeedTypes(items) {
  return _.map(items, t => `${t.travelNeedTypeName},${t.count}`);
}

console.log('TravelNeedTypes:', getTravelNeedTypes(travelNeeds.travelNeeds));

const grpByTravelNeeds = _.countBy(travelNeeds.travelNeeds, 'passengerTypeName');
console.log('CountBy passengerTypeName:', grpByTravelNeeds);


// -------------------- Regex Parsing --------------------

const values = {
  '1': '1 LR',
  '2': '3 ambulatory, 2 wheelchair',
  '3': '2 AP, 2 BP, 2 BR, 2 CN, 2 CR, 2 KS, 2 LR, 2 RP, 2 TS, 2 WD, 2 WK, 2 WS, 5 ambulatory, 2 companion, 2 extraWideLift, 2 liftRequired, 2 nonRampLift, 3 pca, 2 serviceAnimal, 2 serviceAnimals, 5 wheelchair, 2 wideAmbulatory'
};

const result = Object.entries(values).reduce((acc, [, value]) => {
  const count = value.split(',').reduce((sum, item) => {
    const match = item.trim().match(/(\d+)\s*([a-zA-Z]+)/);
    if (!match) return sum;

    const num = parseInt(match[1], 10);
    const unit = match[2].toLowerCase();

    return (unit === 'ambulatory' || unit === 'wheelchair') ? sum + num : sum;
  }, 0);

  acc.push(count);
  return acc;
}, []);

console.log('Result-->', result);


// -------------------- Object Helpers --------------------

const pairs = [
  ['key1', 'value1'],
  ['key2', 'value2'],
  ['key3', 'value3']
];

console.log('fromPairs:', _.fromPairs(pairs));

const resultString = _.chain(travelNeeds.travelNeeds)
  .groupBy('travelNeedTypeName')
  .map((needs, type) => `${_.sumBy(needs, 'count')} ${type}`)
  .join(', ')
  .value();

console.log('TravelNeeds String:', resultString);


// -------------------- AVLM Events --------------------

function getStopEventReceivedTime(avlmEvents, actionType, stopPointId) {
  const validActionTypes =
    actionType === 'depart'
      ? ['depart', 'accept']
      : ['arrive', 'accept'];

  const validEvents = avlmEvents.filter(
    e => validActionTypes.includes(e.actionType) && e.stopPointId === stopPointId
  );

  const sorted = validEvents.sort((a, b) =>
    moment(b.receivedAt).diff(a.receivedAt)
  );

  if (sorted.length > 0 && sorted[0].actionType !== 'accept') {
    return sorted[0].receivedAt;
  }

  return null;
}

const avlmEvents = JSON.parse(
  '[{"eventType":"REE","actionType":"arrive","receivedAt":"2023-08-25T18:20:00+00:00","stopPointId":"1398"},{"eventType":"REE","actionType":"depart","receivedAt":"2023-08-25T18:20:00+00:00","stopPointId":"1398"},{"eventType":"REE","actionType":"arrive","receivedAt":"2023-08-25T17:19:00+00:00","stopPointId":"1390"},{"eventType":"REE","actionType":"depart","receivedAt":"2023-08-25T17:19:00+00:00","stopPointId":"1390"}]'
);

console.log('Time:', getStopEventReceivedTime(avlmEvents, 'depart', '1390'));


// -------------------- ETA Stops --------------------

const etaStops = {
  'etaroute.stoppoints': [
    { nb: '2023-08-18T15:00:00.000Z', e: '2023-08-18T16:15:46.906Z', x: -73.97335, y: 40.63078, id: 'GP133337' }
  ]
};

console.log('etaStops count:', etaStops['etaroute.stoppoints'].length);
```
