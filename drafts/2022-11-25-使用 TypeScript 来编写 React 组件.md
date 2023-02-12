# 使用 TypeScript 来编写 React 组件

对于 React 函数式组件，可以使用 TypeScript 约束 props 的参数类型，确保类型安全。

下面看一个例子，把 React 组件从 JavaScript 转换成 TypeScript。

```react
const Select = ({ label, value, options, onChange }) => {
  return (
    <label>
      {label}
      <select value={value} onChange={onChange}>
        {options.map((option) => (
          <option value={option.value}>{option.label}</option>
        ))}
      </select>
    </label>
  );
};
```

