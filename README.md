# RelinEclipseToken
RelinEclipseToken.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/**
 * @title BaseChain 신규 토큰 컨트랙트 V3
 * @dev Base 체인에 배포하기 위한 완전히 새로운 ERC20 토큰
 *      이전 모든 버전과 이름, 구조, 공급량이 모두 다르게 작성되었습니다.
 * @author Grok (xAI)
 */
contract RelinEclipseToken {
    
    string public name;
    string public symbol;
    uint8 public decimals;
    uint256 public totalSupply;
    
    address public owner;
    
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

    constructor() {
        name = "Relin Eclipse Token";
        symbol = "RECL";
        decimals = 18;
        owner = msg.sender;
        
        // 초기 공급량: 10억 개 (1,000,000,000)
        uint256 initialSupply = 1_000_000_000 * 10 ** decimals;
        _balances[msg.sender] = initialSupply;
        totalSupply = initialSupply;
        
        emit Transfer(address(0), msg.sender, initialSupply);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, unicode"Owner만 호출할 수 있습니다");
        _;
    }

    function balanceOf(address account) public view returns (uint256) {
        return _balances[account];
    }

    function transfer(address to, uint256 amount) public returns (bool) {
        _transfer(msg.sender, to, amount);
        return true;
    }

    function _transfer(address from, address to, uint256 amount) internal {
        require(from != address(0), unicode"zero address로부터 전송할 수 없습니다");
        require(to != address(0), unicode"zero address로 전송할 수 없습니다");
        require(_balances[from] >= amount, unicode"잔액이 부족합니다");
        
        _balances[from] -= amount;
        _balances[to] += amount;
        
        emit Transfer(from, to, amount);
    }

    function approve(address spender, uint256 amount) public returns (bool) {
        require(spender != address(0), unicode"spender는 zero address일 수 없습니다");
        _allowances[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function allowance(address tokenOwner, address spender) public view returns (uint256) {
        return _allowances[tokenOwner][spender];
    }

    function transferFrom(address from, address to, uint256 amount) public returns (bool) {
        uint256 currentAllowance = _allowances[from][msg.sender];
        require(currentAllowance >= amount, unicode"허용된 금액을 초과했습니다");
        
        _transfer(from, to, amount);
        _allowances[from][msg.sender] = currentAllowance - amount;
        
        return true;
    }

    // ==================== Owner 전용 ====================
    function mint(address to, uint256 amount) public onlyOwner {
        require(to != address(0), unicode"zero address로는 민팅할 수 없습니다");
        _balances[to] += amount;
        totalSupply += amount;
        emit Transfer(address(0), to, amount);
    }

    function transferOwnership(address newOwner) public onlyOwner {
        require(newOwner != address(0), unicode"새로운 owner는 zero address일 수 없습니다");
        emit OwnershipTransferred(owner, newOwner);
        owner = newOwner;
    }

    function renounceOwnership() public onlyOwner {
        emit OwnershipTransferred(owner, address(0));
        owner = address(0);
    }
}
[block:44727715 txIndex:25]from: 0x04C...63aE5to: RelinEclipseToken.(constructor)value: 0 weidata: 0x608...20033logs: 1hash: 0x319...ff8f1
