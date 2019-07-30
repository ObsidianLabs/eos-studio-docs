===========================================
Contract Inspector
===========================================

The Contract Page provides the necessary tools to inspect and debug 
smart contracts. 为了方便同时查看多个合约，EOS Studio 使用了tabs的方式来
支持打开多个合约。你可以点击tab来快速切换想要查看的合约。
同时，可以将常用的合约Account加星。

Just below the tabs, there is an address bar where 让你来输入合约账户名。
EOS Studio会根据你输入的合约账户，自动读取abi file in the account来检查合约。

If a smart contract is found in the account, EOS Studio will parse 
the abi file to visualize its actions and tables.

Actions
===========================================

Actions显示在右边。可以通过dropdown menu来切换想调用的action.

-------------------------------------------
Form for Input Parameter
-------------------------------------------

A form for inputs will be generated from the abi to make
it easier to enter parameters.

action的input包含许多类型，EOS Studio会根据类型来处理输入的参数:

- For type of ``uint64_t``, ``uint32_t``, 
- For type of ``permission``, 

You can view the raw transaction command by clicking the 
`View Command` button. It will tell you what command,
including the authorization set below,
EOS Studio is going to execute when you press the `Run` button.
It will show you both the ``cleos`` command and the ``eosjs`` script.

-------------------------------------------
Authorization
-------------------------------------------

在这里可以change ``actor`` and ``permission`` to sign the transaction. By default,
EOS Studio will use ``{account}@active`` which ``account`` is the
current selected account.

EOS Studio暂时不支持multisig.

-------------------------------------------
Ricardian
-------------------------------------------

-------------------------------------------
Result
-------------------------------------------

调用合约后的结果将显示在这里。如果成果了，会看到transaction hash,
点击可以看到完整的transaction details. 如果交易失败了，可以看到
error message.

-------------------------------------------
Execution History and Bookmarks
-------------------------------------------

调用合约的记录（包括参数和结果）会被保存在history中，方便之后查询。
另外，you can add common used parameters to `bookmarks`.

Tables
===========================================

Tables显示在右边。可以通过dropdown menu来切换想查看的table.

