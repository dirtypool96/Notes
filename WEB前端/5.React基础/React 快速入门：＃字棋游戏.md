## 前言

根据react的快速入门来学习react

- 搭建开发环境
- 概览

## 搭建本地开发环境

1. 创建新的react应用

   Create React App是一个用于**学习 React** 的舒适环境，也是用 React 创建**新的单页应用**的最佳方式。

   它会配置你的开发环境，以便使你能够使用最新的 JavaScript 特性，提供良好的开发体验，并为生产环境优化你的应用程序。你需要在你的机器上安装 Node >= 8.10 和 npm >= 5.6。要创建项目，请执行：

   ```shell
   npx create-react-app my-app
   cd my-app
   npm start
   npm run build //在 build 文件夹内生成你应用的优化版本
   ```

2. 删除掉新项目中 `src/` 文件夹下的所有文件。

3. src下新建index.css和index.js

   index.css：

   ```css
   body {
       font: 14px "Century Gothic", Futura, sans-serif;
       margin: 20px;
   }
   
   ol, ul {
       padding-left: 30px;
   }
   
   .board-row:after {
       clear: both;
       content: "";
       display: table;
   }
   
   .status {
       margin-bottom: 10px;
   }
   
   .square {
       background: #fff;
       border: 1px solid #999;
       float: left;
       font-size: 24px;
       font-weight: bold;
       line-height: 34px;
       height: 34px;
       margin-right: -1px;
       margin-top: -1px;
       padding: 0;
       text-align: center;
       width: 34px;
   }
   
   .square:focus {
       outline: none;
   }
   
   .kbd-navigation .square:focus {
       background: #ddd;
   }
   
   .game {
       display: flex;
       flex-direction: row;
   }
   
   .game-info {
       margin-left: 20px;
   }
   ```

   index.js：

   ```js
   import React from 'react';
   import ReactDOM from 'react-dom';
   import './index.css';
   
   class Square extends React.Component {
       render() {
       return (
           <button className="square">
             {/* TODO */}
           </button>
       );
       }
   }
   
   class Board extends React.Component {
       renderSquare(i) {
       return <Square />;
       }
   
       render() {
       const status = 'Next player: X';
   
       return (
           <div>
           <div className="status">{status}</div>
           <div className="board-row">
               {this.renderSquare(0)}
               {this.renderSquare(1)}
               {this.renderSquare(2)}
           </div>
           <div className="board-row">
               {this.renderSquare(3)}
               {this.renderSquare(4)}
               {this.renderSquare(5)}
           </div>
           <div className="board-row">
               {this.renderSquare(6)}
               {this.renderSquare(7)}
               {this.renderSquare(8)}
           </div>
           </div>
       );
       }
   }
     class Game extends React.Component {
       render() {
         return (
           <div className="game">
             <div className="game-board">
               <Board />
             </div>
             <div className="game-info">
               <div>{/* status */}</div>
               <ol>{/* TODO */}</ol>
             </div>
           </div>
         );
       }
     }
     
     // ========================================
     
     ReactDOM.render(
       <Game />,
       document.getElementById('root')
     );
     
   ```

   Square 是一个 **React 组件类**，或者说是一个 **React 组件类型**。一个组件接收一些参数，我们把这些参数叫做 `props`（“props” 是 “properties” 简写），然后通过 `render` 方法返回需要展示在屏幕上的视图的层次结构。

   `render` 方法的返回值*描述*了你希望在屏幕上看到的内容。React 根据描述，然后把结果展示出来。更具体地来说，`render` 返回了一个 **React 元素**，这是一种对渲染内容的轻量级描述。大多数的 React 开发者使用了一种名为 “JSX” 的特殊语法，JSX 可以让你更轻松地书写这些结构。语法 `<div />` 会被编译成 `React.createElement('div')`。

   ### 阅读初始代码
   
   通过阅读代码，你可以看到我们有三个 React 组件：

   - Square
   - Board
   - Game
   
   Square 组件渲染了一个单独的 <button>。Board 组件渲染了 9 个方块。Game 组件渲染了含有默认值的一个棋盘，我们一会儿会修改这些值。到目前为止还没有可以交互的组件。

