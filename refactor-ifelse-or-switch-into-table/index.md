# 重构 if-else 或 switch 成 table


## if-else❌

```javascript
if (season === "summer") {
  return "It's 30, hot!";
} else if (season === "winter") {
  return "It's 2, cold!";
} else {
  return "It's 20, good!";
}
```

## switch❌

```javascript
switch (season) {
	case "summer": return "It's 30, hot!";
	case "winter": return "It's 2, cold!";
	default: return "It's 20, good!";
}
```

## table✅

```javascript
const seasonConfig = {
	summer: { text: "hot!", temperature: 30 },
	winter: { text: "cold!", temperature: 2 },
	defaultKey: { text: "good!", temperature: 20 }
};
function getValueFromTable(table, key, defaultKey) {
	return table[key] || table[defaultKey];
}
const summer = getValueFromTable(seasonConfig, "summer", "defaultKey");
summerDOM.innerHTML = `It's ${summer.temperature}, ${summer.text}`;
```

## Why table✅?

因为后续加 `case` 的时候只需要在 `table` 加就行了！

而且数据和逻辑分开了！

## Why named "Table"?

**Code**

```javascript
const table = {
	summer: { text: "hot!", temperature: 30 },
	winter: { text: "cold!", temperature: 2 },
	defaultKey: { text: "good!", temperature: 20 }
};
```
**Table**

| Key        | Text    | Temperature |
| ---------- | ------- | ----------- |
| summer     | "hot!"  | 30          |
| winter     | "cold!" | 2           |
| defaultKey | "good!" | 20          |

这不是很 `Table` 嘛！

## Codepen Example

{{< codepen id="mdRLVXy" height=600 >}}
