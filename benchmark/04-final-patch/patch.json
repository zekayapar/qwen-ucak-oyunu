{
  "replacements": [
    {
      "old": "  window.addEventListener('keydown', e=>{\n    const k=e.key.toLowerCase();",
      "new": "  window.addEventListener('keydown', e=>{\n    const k=e.key.toLowerCase();\n    if(k==='enter' && e.target.tagName==='BUTTON') return;"
    },
    {
      "old": "    ctx.textAlign='right';\n    ctx.font='700 15px system-ui,sans-serif';\n    ctx.fillStyle='#ffd23f';\n    let hearts='';\n    for(let i=0;i<3;i++) hearts += i<player.lives ? '♥ ' : '♡ ';\n    ctx.fillText('CAN '+hearts, W-14, 40);",
      "new": "    ctx.textAlign='left';\n    ctx.font='700 15px system-ui,sans-serif';\n    ctx.fillStyle='#ffd23f';\n    let hearts='';\n    for(let i=0;i<3;i++) hearts += i<player.lives ? '♥ ' : '♡ ';\n    ctx.fillText('CAN '+hearts, 14, 62);"
    }
  ]
}