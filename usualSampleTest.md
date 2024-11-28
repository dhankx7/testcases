
```solidity
function testYieldDistributionWithSmallDeposits() public {
        uint256 aliceinitialDeposit = 1e13; //alice deposits before the yield starts
        uint256 bobinitialDeposit = 1e8;    //bob is going to deposit after 24hrs when the yield has started
        uint256 yieldAmount = 24000e18;

        console.log("total supply before Alice's deposit ",usualX.totalSupply());
        console.log("total assets before Alice's deposit ",usualX.totalAssets());
        vm.prank(admin);
        usualToken.mint(alice, aliceinitialDeposit);
        vm.startPrank(alice);
        usualToken.approve(address(usualX), aliceinitialDeposit);
        usualX.deposit(aliceinitialDeposit, alice);
        vm.stopPrank();
        uint256 aliceShare = usualX.balanceOf(alice);
        console.log("total assets after Alice's deposit ",usualX.totalAssets());
        console.log("shares Alice ",aliceShare);
        
        uint256 startTime = block.timestamp + 1 hours;
        uint256 endTime = startTime + 1 days; 

        vm.prank(admin);
        usualToken.mint(address(usualX), yieldAmount);
        vm.prank(distributionModuleAddress);
        usualX.startYieldDistribution(yieldAmount, startTime, endTime);

        vm.warp(startTime + 24 hours);
        console.log("total supply before bobs's deposit ",usualX.totalSupply());
        console.log("total assets before bobs deposit",usualX.totalAssets());
        vm.prank(admin);
        usualToken.mint(bob, bobinitialDeposit);
        vm.startPrank(bob);
        usualToken.approve(address(usualX), bobinitialDeposit);
        usualX.deposit(bobinitialDeposit, bob);
        vm.stopPrank();

        uint256 bobShare = usualX.balanceOf(bob);
        console.log("total assets after bobs deposit ",usualX.totalAssets());
        console.log("shares Bobs ",bobShare);

        uint256 bobsWithdrawableAmount = usualX.convertToAssets(bobShare);
        console.log("Assets owed to bob",bobsWithdrawableAmount);
    }
```
```
Logs:
  total supply before Alice's deposit  0
  total assets before Alice's deposit  0
  total assets after Alice's deposit  10000000000000
  shares Alice  10000000000000
  total supply before bobs's deposit  10000000000000
  total assets before bobs deposit 24000000010000000000000
  total assets after bobs deposit  24000000010000100000000
  shares Bobs  0
  Assets owed to bob 0
```
