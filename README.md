-- FPS OPTIMIZER | PROTECTED BUILD
-- Criador: Frostzn
-- Protection Level: MAX

local _k="Frostzn_Protected_Key_120FPS"
local function _x(d,k)
	local r={}
	for i=1,#d do
		local c=d:byte(i)
		local kk=k:byte((i-1)%#k+1)
		r[i]=string.char(bit32.bxor(c,kk))
	end
	return table.concat(r)
end

local _p={
23,57,44,44,52,118,45,36,40,55,34,57,37,52,61,32,118,57,55,52,45,40,55,34,
57,118,32,55,36,44,52,61,57,36,32,55,52,44,57,118,45,55,44,32,57,44,44,
52,61,118,32,55,36,44,52,61,57,36,32,55,52,44,57,118,32,55,36,44,52,61,
57,36,32,55,52,44,57,118,36,44,52,61,57,36,32,55,52,44,57
}

local _s={}
for i=1,#_p do
	_s[i]=string.char(_p[i])
end

local _e=table.concat(_s)
local _d=_x(_e,_k)

loadstring(_d)()
